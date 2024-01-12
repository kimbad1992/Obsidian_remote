```java
@Service
public class RestServiceFactory {
    protected Logger logger = LoggerFactory.getLogger(this.getClass());
    
    public static Map<String, RestService> services = new HashMap<>();
    
    @Autowired
    public RestServiceFactory(List<RestService> restServices) {
        for (RestService service : restServices) {
            services.put(service.getServiceId(), service);
            logger.debug("created rest service: {}", service.getServiceId());
        }
    }
    
    public RestService getService(String brand, String service) throws CommonException {
        String serviceId = ComUtils.getServiceId(brand, service);
        return getService(serviceId);
    }
    
    public RestService getService(String serviceId) throws CommonException {
        if (services.containsKey(serviceId)) {
            logger.debug("get service: {}", serviceId);
            return services.get(serviceId);
        }
        throw new CommonException(ErrorCode.ERR_6003);
    }

    /**
     * 지역별(프로퍼티의 region) 유저 정보 RestService를 반환합니다.
     * @param brand 차량 브랜드
     * @return RestService 서비스 지역의 계정 관련 RestService를 반환
     * @throws CommonException
     */
    public RestService getLoginModule(String brand) throws CommonException {
        String systemRegion = ComUtils.nvl(Info.getProperty("system.env.region"), Constant.EU);

        // 유럽 프로젝트
        if (ComUtils.isEqual(systemRegion, Constant.EU))
            return getService(brand, Constant.REST_API_KIA_ACCOUNT);
        // 한국 프로젝트
        else if (ComUtils.isEqual(systemRegion, Constant.KO))
            return getService(brand, Constant.REST_API_CCSP);
        // TODO : OpenID Kia Account TEST용
        else if (ComUtils.isEqual(systemRegion, "OPENID"))
            return getService(brand, "openid");
        // 기타
        else
            return getService(brand, Constant.REST_API_KIA_ACCOUNT);
    }
}
```