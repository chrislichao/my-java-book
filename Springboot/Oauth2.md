# Oauth2

##### 1，工作原理
- 核心思想：用户使用用户名和密码登录系统后，客户端会收到一对令牌，这是一个访问权限令牌和刷新令牌。访问令牌用于访问系统中的所有服务，到期后，系统使用刷新令牌生成一对新的令牌，所以，如果用户每天都进入系统，令牌也会每天更新，不需要每次都用用户名和密码登录。刷新令牌也有它的过期时间（虽然它比访问令牌长得多），如果一个用户一年没有进入系统，那么很可能会被要求重新输入用户名和密码
- 参考文章：[OAuth 2.0 - 阮一峰](http://www.ruanyifeng.com/blog/2019/04/oauth_design.html)
- 使用步骤
  - 配置资源服务器
  - 配置认证服务器
  - 配置Spring Security
- 使用场景
  - 授权码模式（authorization code），使用了回调地址，最为复杂，通常QQ第三方登录等会使用
  - 简化模式（implicit），不常用
  - 密码模式（resource owner password credentials），常用，系统已经存在用户体系，且有权限，一般采用本模式
  - 客户端模式（client credentials），常用，仅仅做系统间对接，不考虑用户，一般采用本模式
##### 2，配置maven依赖
```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.security.oauth</groupId>
            <artifactId>spring-security-oauth2</artifactId>
            <version>2.3.6.RELEASE</version>
        </dependency>
```
##### 3，创建常量类
```java
/**
 * [OAuth2常量类]
 */
@Component
public class OAuth2Constant {
    /**
     * 客户端
     */
    public static final String OAUTH_CLIENT = "nsd";
    /**
     * 受保护资源
     */
    public static final String OAUTH_RESOURCE_ID = "nsd_platform";
    /**
     * 授权类型-密码
     */
    public static final String OAUTH_GRANT_TYPE_PASSWORD = "password";
    /**
     * 授权类型-刷新令牌
     */
    public static final String OAUTH_GRANT_TYPE_REFRESH_TOKEN = "refresh_token";
    /**
     * 限制客户端的访问范围-所有
     */
    public static final String OAUTH_SCOPES_ALL = "all";
    /**
     * 客户端可以使用的权限
     */
    public static final String OAUTH_AUTHORITIES_OAUTH2 = "oauth2";
    /**
     * 客户端安全码
     */
    public static final String OAUTH_SECRET = "nsd_pwd";
    /**
     * token有效时间
     */
    public static int ACCESS_TOKEN_VALIDITY_SECONDS = 3600;
    /**
     * refresh token 有效时间
     */
    public static int REFRESH_TOKEN_VALIDITY_SECONDS = 43200;
    /**
     * 在header里提交时必须加这个开头单词
     */
    public static final String ACCESS_TOKEN_START_PORTION = "Bearer";

    public static final String DEFAULT_PWD = "123456";


    @Value("${oauth.token.access_token_validity_seconds}")
    private void setAccessTokenValiditySeconds(int access_token_validity_seconds){
        ACCESS_TOKEN_VALIDITY_SECONDS = access_token_validity_seconds;
    }

    @Value("${oauth.token.refresh_token_validity_seconds}")
    private void setRefreshTokenValiditySeconds(int refresh_token_validity_seconds){
        REFRESH_TOKEN_VALIDITY_SECONDS = refresh_token_validity_seconds;
    }

}
```
##### 3，自定义Token异常
```java
/**
 * [自定义Token异常]
 *
 * @author Chris Li[黎超]
 * @create [2020/10/30]
 */
public class AuthExceptionEntryPoint implements AuthenticationEntryPoint {

    private static final Logger logger = LoggerFactory.getLogger(AuthExceptionEntryPoint.class);

    @Override
    public void commence(HttpServletRequest request, HttpServletResponse response,
                         AuthenticationException authException) throws ServletException {
        response.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
        response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
        try {
            ObjectMapper mapper = new ObjectMapper();
            mapper.writeValue(response.getOutputStream(), ResponseBaseVo.fail(ErrorCodeEnum.COMMON_USER_NOT_LOGIN));
        } catch (IOException e) {
            logger.error("自定义AuthExceptionEntryPoint输出流异常{}", e);
            throw new ServletException();
        }
    }
}
```
##### 4，配置资源服务器
```java
/**
 * [资源服务器]
 *
 * @author Chris Li[黎超]
 * @create [2020/10/30]
 */
@Configuration
@EnableResourceServer
@Order(3)
public class ResourceServerConfig extends ResourceServerConfigurerAdapter {

    private static final Logger logger = LoggerFactory.getLogger(ResourceServerConfig.class);

    private static String antMatcherPath;

    @PostConstruct
    public void init() {
        if (System.getProperties().getProperty("os.name").startsWith("Mac")) {
            antMatcherPath = "/none/**";
        } else {
            antMatcherPath = "/api/**";
        }
    }

    @Override
    public void configure(ResourceServerSecurityConfigurer resources) {
        resources.resourceId(OAuth2Constant.OAUTH_RESOURCE_ID).stateless(true).authenticationEntryPoint(new AuthExceptionEntryPoint());
    }

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.headers().frameOptions().disable();
        http.cors().and().csrf().disable();
        http
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)
                .and().exceptionHandling()
                .authenticationEntryPoint((req, resp, exception) -> {
                    PrintWriter writer = null;
                    resp.setContentType(MediaType.APPLICATION_JSON_UTF8_VALUE);
                    resp.setStatus(HttpServletResponse.SC_UNAUTHORIZED);
                    try {
                        writer = resp.getWriter();
                        writer.print(JSON.toJSON(ResponseBaseVo.fail(ErrorCodeEnum.COMMON_USER_NOT_LOGIN)));
                    } catch (IOException e) {
                        logger.error("自定义AuthenticationEntryPoint输出流异常{}", e);
                    } finally {
                        if (writer != null) {
                            writer.close();
                        }
                    }
                })
                .and().anonymous()
                .and().authorizeRequests()
                .antMatchers(antMatcherPath).authenticated()
                .requestMatchers(CorsUtils::isPreFlightRequest)
                .permitAll();
    }
}
```
##### 5，自定义RedisTokenStore
```java
/**
 * [自定义RedisTokenStore]
 *
 * @author Chris Li[黎超]
 * @create [2020/10/30]
 */
public class MyRedisTokenStore extends RedisTokenStore {

    public MyRedisTokenStore(RedisConnectionFactory connectionFactory) {
        super(connectionFactory);
    }

    @Override
    public OAuth2AccessToken getAccessToken(OAuth2Authentication authentication) {
        OAuth2AccessToken oAuth2AccessToken = super.getAccessToken(authentication);
        if (oAuth2AccessToken != null) {
            OAuth2AccessToken accessToken = super.readAccessToken(oAuth2AccessToken.getValue());
            if (accessToken != null) {
                if (accessToken.getRefreshToken() != null) {
                    super.removeRefreshToken(accessToken.getRefreshToken());
                }
                super.removeAccessToken(accessToken);
            }
        }
        return null;
    }
}
```
##### 6，配置认证服务器
```java
/**
 * [认证服务器]
 *
 * @author Chris Li[黎超]
 * @create [2020/10/30]
 */
@Configuration
@EnableAuthorizationServer
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

    @Autowired(required = false)
    AuthenticationManager authenticationManager;

    @Autowired(required = false)
    RedisConnectionFactory redisConnectionFactory;

    @Autowired(required = false)
    UserDetailsService userDetailsService;

    @Override
    public void configure(ClientDetailsServiceConfigurer clients) throws Exception {
        clients.inMemory().
                withClient(OAuth2Constant.OAUTH_CLIENT)
                .resourceIds(OAuth2Constant.OAUTH_RESOURCE_ID)
                .authorizedGrantTypes(OAuth2Constant.OAUTH_GRANT_TYPE_PASSWORD, OAuth2Constant.OAUTH_GRANT_TYPE_REFRESH_TOKEN)
                .scopes(OAuth2Constant.OAUTH_SCOPES_ALL)
                .authorities(OAuth2Constant.OAUTH_AUTHORITIES_OAUTH2)
                .secret(OAuth2Constant.OAUTH_SECRET)
                .accessTokenValiditySeconds(OAuth2Constant.ACCESS_TOKEN_VALIDITY_SECONDS)
                .refreshTokenValiditySeconds(OAuth2Constant.REFRESH_TOKEN_VALIDITY_SECONDS);
    }

    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) {
        endpoints.tokenStore(new MyRedisTokenStore(redisConnectionFactory))
                .authenticationManager(authenticationManager).userDetailsService(userDetailsService)
                // 允许 GET、POST 请求获取 token，即访问端点：oauth/token
                .allowedTokenEndpointRequestMethods(HttpMethod.GET, HttpMethod.POST);
        endpoints.reuseRefreshTokens(true);
    }

    @Override
    public void configure(AuthorizationServerSecurityConfigurer oauthServer) {
        // 允许表单认证
        oauthServer.allowFormAuthenticationForClients();
        //允许接口/oauth/check_token 被调用
        oauthServer.tokenKeyAccess("permitAll()").checkTokenAccess("permitAll()");
    }

}
```