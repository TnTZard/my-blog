---
title: Spring的注解相关
date: 2020-06-05 09:36:52
author: TnTZard
tags: Spring 
---

# 移除@Autowired

```java
@Slf4j
@Validated
@RestController
@RequiredArgsConstructor
@RequestMapping("user")
public class UserController {
    // final 确保依赖不为null 单例还有线程问题
    private final IUserService userService;
}

```


hello


