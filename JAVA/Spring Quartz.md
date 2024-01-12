
# Quartz 스케쥴러를 사용한 Batch 동적 관리

## QuartzConfig

```java
@Configuration  
public class QuartzConfig {  
  
    @Autowired  
    private ApplicationContext applicationContext;  
  
    @Bean  
    public BatchLoggingListener batchLoggingListener() {  
        return new BatchLoggingListener();  
    }  
  
    @Bean  
    public SchedulerFactoryBean schedulerFactoryBean(DataSource dataSource) {  
        /**  
         * TODO : 쿼츠 JDBCStore || Misfire || 클러스터링 사용 시 쿼츠에서 사용할 DB 릴레이션이 필요함  
         * QRTZ_JOB_DETAILS: 작업에 대한 세부 정보를 저장합니다.  
         * QRTZ_TRIGGERS: 트리거에 대한 세부 정보를 저장합니다.  
         * QRTZ_CRON_TRIGGERS: 크론 트리거에 대한 세부 정보를 저장합니다.  
         * QRTZ_SIMPLE_TRIGGERS: 단순 트리거(반복 횟수와 간격을 가진)에 대한 세부 정보를 저장합니다.  
         * QRTZ_SIMPROP_TRIGGERS: SimplePropertiesTrigger에 대한 세부 정보를 저장합니다.  
         * QRTZ_BLOB_TRIGGERS: BLOB 타입의 트리거 데이터를 저장합니다.  
         * QRTZ_CALENDARS: Quartz의 Calendar 객체에 대한 정보를 저장합니다.  
         * QRTZ_PAUSED_TRIGGER_GRPS: 일시 중지된 트리거 그룹 목록을 저장합니다.  
         * QRTZ_LOCKS: 클러스터링을 위한 락 정보를 저장합니다.  
         * QRTZ_SCHEDULER_STATE: 클러스터 내 다른 스케줄러 인스턴스의 상태를 저장합니다.  
         */  
        SchedulerFactoryBean schedulerFactoryBean = new SchedulerFactoryBean();  
  
        // Batch를 Job 인터페이스의 구현체로 만들 경우 Spring DI 사용을 위해 설정해야함  
        AutowiringSpringBeanJobFactory jobFactory = new AutowiringSpringBeanJobFactory();  
        jobFactory.setApplicationContext(applicationContext);  
        schedulerFactoryBean.setJobFactory(jobFactory);  
  
        // schedulerFactoryBean.setDataSource(dataSource);  
  
        // 스케쥴러 이름 설정  
        // schedulerFactoryBean.setSchedulerName("MyScheduler");  
  
        // 쿼츠 상세 프로퍼티  
        Properties quartzProperties = new Properties();  
  
        // 쓰레드풀 설정  
        quartzProperties.put("org.quartz.threadPool.threadCount", "1");  
  
        // JDBCJobStore 설정  
        // quartzProperties.put("org.quartz.jobStore.class", "org.quartz.impl.jdbcjobstore.JobStoreTX");  
        // quartzProperties.put("org.quartz.jobStore.dataSource", "myDS");  
        // 클러스터링 설정 (JDBCJobStore 활성화시 사용 가능)  
        // quartzProperties.put("org.quartz.jobStore.isClustered", "true");        // quartzProperties.put("org.quartz.jobStore.clusterCheckinInterval", "20000");  
        // 트리거 히스토리 (Job 트리거시 로깅)  
        // quartzProperties.put("org.quartz.plugin.triggHistory.class", "org.quartz.plugins.history.LoggingTriggerHistoryPlugin");        // quartzProperties.put("org.quartz.plugin.triggHistory.triggerFiredMessage", "Trigger {1}.{0} fired job {6}.{5} at: {4, date, HH:mm:ss MM/dd/yyyy}");        // quartzProperties.put("org.quartz.plugin.triggHistory.triggerCompleteMessage", "Trigger {1}.{0} completed firing job {6}.{5} at {4, date, HH:mm:ss MM/dd/yyyy}");  
        // Misfire 처리 전략 변경 (제 시간에 Job이 트리거되지 못한 경우)  
        // quartzProperties.put("org.quartz.jobStore.misfireThreshold", "60000");  
        schedulerFactoryBean.setQuartzProperties(quartzProperties);  
  
        return schedulerFactoryBean;  
    }  
  
    @Bean  
    public Scheduler scheduler(SchedulerFactoryBean schedulerFactoryBean) throws Exception {  
        Scheduler scheduler = schedulerFactoryBean.getScheduler();  
        scheduler.start();  
        return scheduler;  
    }  
  
    // Job 내 오토와이어링 사용이 가능하도록 JobFactory 설정  
    public static class AutowiringSpringBeanJobFactory extends SpringBeanJobFactory {  
        private AutowireCapableBeanFactory beanFactory;  
  
        @Override  
        public void setApplicationContext(final ApplicationContext context) {  
            beanFactory = context.getAutowireCapableBeanFactory();  
        }  
  
        @Override  
        protected Object createJobInstance(final TriggerFiredBundle bundle) throws Exception {  
            final Object job = super.createJobInstance(bundle);  
            beanFactory.autowireBean(job);  
            return job;  
        }  
    }  
}
```

각 Batch 단위 서비스가 될 Job 클래스에서 Autowired를 사용하지 못하는 문제로
JobFactory를 추가로 설정해야함

Misfire 정책이나 JobStore 사용 여부에 따라서 쿼츠에서 사용할 DB 릴레이션이 필요한 경우가 있음

## Job 클래스

```java
@Service  
@Slf4j  
public class SomeJob extends QuartzJobBean {  

    @Autowired  
    private SomeService someService;  
  
    @Override  
    @Transactional    
    // @BatchJob("someService") // Aspect의 감지용 어노테이션
    public void executeInternal(JobExecutionContext jobExecutionContext) throws JobExecutionException {  
       try {  
		  someService.someMethod(...);
       } catch (Exception e) {  
          log.error("some Error...");  
          throw new YourException(e);  
       }  
    }  
}
```

Job 인터페이스의 구현체인 추상 클래스 QuartzJobBean을 확장해서 사용한다
추상 메서드 `excuteInternal()`를 구현하게 된다.

### Batch Logging

```java
@Component  
@Aspect  
@Slf4j  
public class BatchLoggingAspect {  
  
    @Autowired  
    BatchScheduleService batchScheduleService;  
  
    @Autowired  
    BatchLogService batchLogService;
  
    private final static String INPROCESS = "INPROCESS";  
    private final static String COMPLETED = "COMPLETED";  
    private final static String SUCCESS = "SUCCESS";  
    private final static String FAIL = "FAIL";  
  
    @Pointcut("execution(* com.nsc.rest.batch.*Job.*(..)) && @annotation(batchJob)")  
    private void batchJobMethods(BatchJob batchJob) {  
    }  
    @Around(value = "batchJobMethods(batchJob)", argNames = "joinPoint,batchJob")  
    public Object aroundBatchJob(ProceedingJoinPoint joinPoint, BatchJob batchJob) throws Throwable {  
        // 로깅 로직  
        doLogging(joinPoint, batchJob);  
        return joinPoint.proceed();  
    }  
  
    public void doLogging(ProceedingJoinPoint joinPoint, BatchJob batchJob) throws Throwable {  
  
        ...
  
        try {  
            // @Before 수행  
	        ...
            // @Before 종료  
  
  
            // Target 메서드 호출  
            Object result = joinPoint.proceed();  
            // Target 메서드 종료  
  
  
            // @AfterReturning 수행  
            ...
            // @AfterReturning 종료  
        } catch (Exception e) {  
            // @AfterThrowing 수행  
			...
            throw e;  
            // @AfterThrowing 종료  
        } finally {  
            // @After 수행  
            ...
            // @After 종료  
        }  
  
    }  
}
```

기존에 Batch Logging을 하고 해당 Batch의 실행 상태를 DB에 저장하는 Aspect다.
Spring 레거시 프로젝트를 Spring Boot로 업팩토링을 진행하며 한가지 문제가 발생했다.
Spring AOP는 Proxy 형태로 동작하게 되는데, `QuartzJobBean`의 `executeInternal()`메서드는
protected 접근자여서 Proxy가 동작할 수 없다는 것.
그래서 변경한 형태가 Job Listner다.

### Job Listener

```java
@Slf4j
public class BatchLoggingListener implements JobListener {

    @Autowired
    private BatchScheduleService batchScheduleService;

    @Autowired
    private BatchLogService batchLogService;

    private final static String INPROCESS = "INPROCESS";
    private final static String COMPLETED = "COMPLETED";
    private final static String SUCCESS = "SUCCESS";
    private final static String FAIL = "FAIL";
    
    @Override
    public String getName() {
        return "BatchLoggingListener";
    }

    @Override
    public void jobToBeExecuted(JobExecutionContext context) {

        // @Before 수행
        ...
        // @Before 종료
    }

    @Override
    public void jobExecutionVetoed(JobExecutionContext jobExecutionContext) {
        // 실행 거절된 경우
    }

    @Override
    public void jobWasExecuted(JobExecutionContext context, JobExecutionException e) {
        ...
        
        // 예외가 발생한 경우 (@AfterThrowing)
        if (e != null && 기타 조건...) {
            ...
        }

        // 예외가 발생하지 않은 경우 (@AfterReturning)
        if (e == null && 기타 조건...) {
            ...
        }

        // @After 수행
        ...
        // @After 종료
    }

    private String getMethodName(JobExecutionContext context) {
        return context.getJobDetail().getKey().getName();
    }
}
```

@After, @Before등 각 AOP 기능에 대해 1:1 매칭이 되지는 않지만, 
각 메서드 구현을 통해 Quartz Job 실행 전/후 등으로 특정 로직 실행이 가능하다.

## Scheduler Initializer

```java
@Component  
@Slf4j  
public class SchedulerInitializer {  
  
    @Autowired  
    private BatchScheduleService batchScheduleService;  
  
    @Autowired  
    private BatchLoggingListener batchLoggingListener;  
  
    @Autowired  
    private Scheduler scheduler;  
    private Map<String, JobKey> jobsMap = new HashMap<>();  
  
    @PostConstruct  
    public void init() {  
        try {  
            // 배치 스케줄 정보 로드  
            List<BatchVO> allBatchSchedules = batchScheduleService.getBatchSchedule();  
  
            // 스케줄러에 배치 작업 등록  
            for (BatchVO batchVO : allBatchSchedules) {  
                addOrUpdateJob(batchVO);  
            }  
  
            // 스케쥴러에 JobListener 추가  
            scheduler.getListenerManager().addJobListener(batchLoggingListener);
            
        } catch (Exception e) {  
            log.error("Scheduler init Error : {}", e.getMessage(), e);  
            throw new RuntimeException("Scheduler init Error");  
        }  
    }  
  
    public void addOrUpdateJob(BatchVO batchVO) {  
        // 크론 표현식 유효성 확인  
        boolean isCronExpressionValid = CronExpression.isValidExpression(batchVO.getBatchSchedule());  
        if (!isCronExpressionValid) {  
            log.error("[{}] : Cron Expression format is invalid", batchVO.getBatchClass());  
            return;  
        }  
  
        // 새로운 JobDetail과 Trigger를 생성하여 스케줄러에 작업을 등록  
        try {  
            JobKey jobKey = jobsMap.get(batchVO.getBatchId());  
  
            if (jobKey != null && scheduler.checkExists(jobKey)) {  
                // 기존에 동일한 ID로 스케줄링된 작업이 있는지 확인하고, 있다면 해당 작업을 취소  
  
                // 배치 상태가 'N'이거나 삭제 플래그가 'Y'인 경우 작업 삭제  
                if (batchVO.getBatchStatus().equals("N") || batchVO.getBatchDeleteYN().equals("Y")) {  
                    scheduler.deleteJob(jobKey);  
                    jobsMap.remove(batchVO.getBatchId());  
                    return;  
                }  
  
                List<? extends Trigger> triggers = scheduler.getTriggersOfJob(jobKey);  
                CronTrigger existingTrigger = (CronTrigger) triggers.get(0);  
                String currentCronExpression = existingTrigger.getCronExpression();  
                String newCronExpression = batchVO.getBatchSchedule();  
  
                // 현재 크론식과 변경된 크론식이 다른 경우 재스케쥴링  
                if (!newCronExpression.equals(currentCronExpression)) {  
                    Trigger newTrigger = existingTrigger.getTriggerBuilder()  
                            .withSchedule(CronScheduleBuilder.cronSchedule(newCronExpression))  
                            .build();  
                    scheduler.rescheduleJob(existingTrigger.getKey(), newTrigger);  
                }  
            } else {  
                // Batch 활성화 상태 확인  
                if (batchVO.getBatchStatus() == null || !batchVO.getBatchStatus().equals("Y")) {  
                    log.debug("Batch job [{}] is not active", batchVO.getBatchId());  
                    return;  
                }  
  
                Class<? extends Job> jobClass = (Class<? extends Job>) Class.forName(batchVO.getBatchClass());  
                JobDetail jobDetail = JobBuilder.newJob(jobClass)  
                        .withIdentity(batchVO.getBatchId()).build();  
                CronTrigger trigger = TriggerBuilder.newTrigger().withIdentity(batchVO.getBatchId() + "Trigger")  
                        .withSchedule(CronScheduleBuilder.cronSchedule(batchVO.getBatchSchedule())).build();  
                scheduler.scheduleJob(jobDetail, trigger);  
                // JobKey를 jobsMap에 저장  
                jobsMap.put(batchVO.getBatchId(), jobDetail.getKey());  
            }  
        } catch (ClassNotFoundException | SchedulerException e) {  
            log.error("Scheduling Error: {}", e.getMessage(), e);  
            throw new BusinessException(BizCodeMessage.ERROR_3001);  
        } finally {  
            // 로깅..  
        }  
    }  
  
    @PreDestroy  
    public void destroy() {  
        if (scheduler != null) {  
            try {  
                scheduler.shutdown(true);  
            } catch (SchedulerException e) {  
                log.error("Error while shutting down scheduler: {}", e.getMessage(), e);  
                throw new RuntimeException("Error while shutting down scheduler");  
            }  
        }  
    }  

}
```
Scheduler Initializer가 각 의존성 주입이 완료된 후 DB에 등록된 각 Batch Schedule 목록을 읽어  `addOrUpdateJob()` 메서드를 통해 스케쥴러에 추가한다.
Scheduler Initializer는 Bean으로 생성되어 다른 Bean에서도 사용할 수 있다.
예를 들어, **배치 스케쥴을 변경하는 서비스**쪽에서 DB의 Batch Schedule 정보를 변경 한 후
`addOrUpdateJob()` 호출을 통해 스케쥴러의 상태를 변경할 수 있다.