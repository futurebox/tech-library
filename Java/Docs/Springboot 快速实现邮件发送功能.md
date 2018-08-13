# Springboot 快速实现邮件发送功能

------

> 本文主要介绍如何使用SpringBoot自带的spring-boot-starter-mail实现邮件发送功能
> （支持文本格式邮件、html格式邮件、带有多附件的邮件、带有多资源的邮件）

### 1. 引入依赖包

在pom.xml中引入spring-boot-starter-mail依赖包

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-mail</artifactId>
    </dependency>


#### 2. 配置邮件发送参数

在application.properties中加入如下参数：

    spring.mail.host=smtp.xxx.com
    spring.mail.username=xxx@xxx.com
    spring.mail.password=xxxxxx
    spring.mail.properties.mail.smtp.auth=true
    spring.mail.properties.mail.smtp.starttls.enable=true
    spring.mail.properties.mail.smtp.starttls.required=true

如果是application.yml，如下：

    spring:
      mail:
        host: smtp.xxx.com
        username: xxx@xxx.com
        password: xxxxxx
        properties:
          mail:
            smtp:
              auth: true
              starttls:
                enable: true
                required: true

#### 3. 添加代码发送邮件

    /**
	 * 注入MailSender
	 */
	@Autowired
    private JavaMailSender mailSender;
	
	/**
	 * 读取配置文件中的发送用户信息
	 */
	@Value("${spring.mail.username}")
    private String fromEmail;

发送文本格式邮件：

    SimpleMailMessage message = new SimpleMailMessage();
    message.setFrom(fromEmail);
    
    /**
     * 收件人邮件地址
     */
    message.setTo(toEmail);
    message.setSubject(subject);
    message.setText(body);
    mailSender.send(message);
    
发送Html格式/带有多附件/带有多资源的邮件：
    
    MimeMessage message = mailSender.createMimeMessage();
    MimeMessageHelper helper = new MimeMessageHelper(message, true);
    helper.setFrom(fromEmail);
    helper.setTo(toEmail);
    helper.setSubject(subject);
    helper.setText(mailBody, true);
    
    /**
     * 判断附件是否为空
     */
    if(!StringUtils.isEmpty(photos)){
    	/**
    	 * 多附件处理
    	 */
    	photos.entrySet().forEach(entry->{
            try {
        		FileSystemResource file = new FileSystemResource(new File(entry.getValue()));
        		
        		if(isAttachment) {
        			helper.addAttachment(entry.getKey(), file);
        		}else {
					helper.addInline(entry.getKey(),file);
        		}
			} catch (MessagingException e) {
				e.printStackTrace();
				mailSenderModal.setCode(-2);
				mailSenderModal.setMsg(e.getMessage());
			}
    		
    	});
        
    }
    
    mailSender.send(message);
    
### 源码参考地址：
https://github.com/futurebox/SpringBoot/tree/master/MailSender

