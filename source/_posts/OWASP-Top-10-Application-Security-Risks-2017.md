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
- [OWASP Testing Guide: SQL Injection](https://www.owasp.org/index.php/Testing_for_SQL_Injection_(OTG-INPVAL-005)), [Command Injection](https://www.owasp.org/index.php/Testing_for_Command_Injection_(OTG-INPVAL-013)), [ORM injection](https://www.owasp.org/index.php/Testing_for_ORM_Injection_(OTG-INPVAL-007))
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

