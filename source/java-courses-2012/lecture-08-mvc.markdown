---                                                                                                                     
layout: page                                                                                                            
title: JC12 - Lecture 08. MVC

comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Course 2012 ([back](index.html))
## Lecture 08. MVC

## План / тезисы

* MVC pattern;
* handmade MVC.

## Handmade MVC

Практическим заданием на это занятие, а так же домашним заданием является написание простейшего MVC-фреймворка с использованием следующих технологий:

* Servlet API;
* Google Guice;
* template engine (Velocity, FreeMarker);
* json (Jackson JSON, Gson);
* etc.

Далее будет пример использования этого фреймворка (по сути его API), какие то части из этого уже были реализованны на занятии, остальные буду реалищовываться завтра на занятии и в последующей домашней работе.

```
public class TestController extends Controller {
		@Bind(“/index”)
		public void index(@Param(“name”) String name) {
			String hw = “Hello, ” + name;
			renderString(hw);
		}
}
```

```
public interface Result {
    void render(HttpServletRequest req, HttpServletResponse resp);
}
```

* `@Bind` - какой url обрабатывать данному методу;
* `@Param` - имя http-параметра, который будет подставлен в эту переменную, должны поддерживаться все примитивные типы и их обертки;
* `render***` - методы, которые передают данные во `View` по средством передачи результата в объектах реализующих `Result`

Пример реализации интерфейса `Result`
```
public class StringResult implements Result {
    private final String str;

    public StringResult(String str) {
        this.str = str;
    }

    @Override
    public void render(HttpServletRequest req, HttpServletResponse resp) {
        try {
            resp.getWriter().println(str);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
}
```

Необходимо реализовать следующие возможности:

* `renderTemplate(templateName, model)`, `renderJson(mapOrObject)`;
* `@SessionParam`, `@JsonParam`.

