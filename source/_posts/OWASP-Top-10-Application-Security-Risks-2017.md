---
title: OWASP 2017 Web 应用程序十大安全风险(译)
date: 2017-12-11 22:01:18
desc: OWASP 安全 漏洞 Web 2017 Top10
---

原文地址：[OWASP Top 10 Application Security Risks - 2017](https://www.owasp.org/index.php/Top_10_2017-Top_10)

OWASP 发布 Web 应用程序十大安全风险，至今为止应该有三次更新：2010年 / 2013年 / 2017年。

正好工作上在讨论 2013 年的版本，此篇文章用来翻译 2017 年的版本。

见识一下这排名前十的 Web 应用安全风险。

<!--more-->

## Top 10 应用安全风险列表

1. [A1-注入](#A1-注入)
2. [A2-失效的身份认证](A2-失效的身份认证)
3. [A3-敏感数据泄露](#A3-敏感数据泄露)
4. [A4-XML 外部实体 (XXE)](#A4-XML 外部实体 (XXE))
5. [A5-失效的访问控制](#A5-失效的访问控制)
6. [A6-安全配置错误](#A6-安全配置错误)
7. [A7-跨站脚本 (XSS)](#A7-跨站脚本 (XSS))
8. [A8-不安全的反序列化](#A8-不安全的反序列化)
9. [A9-使用含有已知漏洞的组件](#A9-使用含有已知漏洞的组件)
10. [A10-不足的日志记录和监控](#A10-不足的日志记录和监控)

## A1-注入

> 注入缺陷，例如 SQL，NoSQL，OS 和 LDAP 注入，一般指不受信任的数据被伪装成命令或者查询语句的一部分，发送至解析器后发生了执行的过程。攻击者的恶意数据能够欺骗解释器在未被授权的情况下执行非预期代码或者访问数据。

#### 威胁来源、弱点以及影响

几乎任何数据来源都可能成为注入攻击的媒介，包括环境变量，参数，外部以及内部的 Web 服务，以及用户的类型。当攻击者可以向解释器发送恶意数据时，注入漏洞就产生了。

注入漏洞十分盛行，特别是在历史遗留的代码中。注入缺陷通常出现在 SQL，LDAP，XPath 或者 NoSQL 查询语句，OS 命令，XML 解析器，SMTP 头部，表达式语言，以及 ORM 查询语句中。注入漏洞很容易被代码审查发现。扫描器和模糊搜索可以帮助攻击者找到这些注入漏洞。

注入可能会导致数据丢失、破坏以及公开给未授权的群组，责任缺失以及拒绝授权。注入有些时候会导致主机被彻底地接管。为避免业务受到影响，应该重视应用与数据的保护。

#### 你的应用是脆弱的吗？

一个应用是脆弱的如果它：

* 对用户来源的数据不进行验证，过滤或者净化。
* 动态查询或者非参数的调用，在没有上下文感知转义的情况下，被用于解释器。
* 恶意数据被直接赋给 ORM 查询参数，用于获取敏感或者未授权的数据。
* 恶意数据被直接使用或者连接，例如 SQL 或者 命令在动态查询语句、命令或者存储过程中包含结构和恶意数据。

#### 攻击案例场景

**例子一**：一个应用将不可信的数据用于 SQL 查询语句结构中：

```sql
String query = "SELECT * FROM accounts WHERE custID='" + request.getParameter("id") + "'";
```

**例子二**：类似的，应用盲目地信任框架工具，仍然可能导致查询语句的漏洞：

```Sql
Query HQLQuery = session.createQuery("FROM accounts WHERE custID='" + request.getParameter("id") + "'");
```

在这两个例子中，攻击者在他们的浏览器中修改 `id` 参数的值变成 ` or '1' = '1'` ，例如：

```
http://example.com/app/accountView?id=' or '1'='1
```

这样变动意味着这两个查询语句俊辉返回用户表中的所有记录。更多危险的攻击可能篡改或者删除数据，甚至存储过程被调用。

#### 如何防御

防御注入攻击需要保持数据与命令语句、查询语句的分隔开来。

* 最好的选择是使用安全的 API，完全避免使用解释器，或者提供一个参数界面的接口，或者迁移到 ORM 或者实体框架。
* 使用正确的或者*白名单*服务器端的输入验证，均有助于防止注入攻击。但这不是一种彻底的防御方式，因为很多应用程序都需要特殊的字符，比如文本区域或者移动端的 API 设计。
* 对于那些历史残留的动态查询，可以使用这个解释器的特殊字符转义语法来处理。
* 查询语句中使用 LIMIT 和其他 SQL 的控件，来阻止 SQL 注入时大量地泄露记录。

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



