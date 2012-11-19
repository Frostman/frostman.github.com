---                                                                                                                     
layout: page                                                                                                            
title: JC12 - Lecture 06. WEB #1: HTTP, HTML, CSS, Servlet API

comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Course 2012 ([back](index.html))
## Lecture 06. WEB #1: HTTP, HTML, CSS, Servlet API

## План / тезисы

* small intro to http, html, css
* servlet api
* how to start jetty from IDEA

## HTTP

HTTP - HyperText Transfer Prоtocоl

### HTTP/1.0

В мае 1996 года был выпущен RFC 1945, послуживший основой для реализации большинства компонент HTTP/1.0.

### HTTP/1.1

Текущая версия протокола, принята в июне 1999 года. Новым в этой версии был режим «постоянного соединения»: TCP-соединение может оставаться открытым после отправки ответа на запрос, что позволяет посылать несколько запросов за одно соединение. Клиент теперь обязан посылать информацию об имени хоста, к которому он обращается, что сделало возможной более простую организацию виртуального хостинга.

### Структура HTTP протокола

Каждое HTTP-сообщение состоит из 3 частей:

1. Starting line — определяет тип сообщения;
2. Headers — характеризуют тело сообщения, параметры передачи и прочие сведения;
3. Message Body — непосредственно данные сообщения, отделяется от заголовков пустой строкой.

### HTTP request (открытие странички про HTTP на wikipedia.org)

```
GET /wiki/HTTP HTTP/1.0
Host: ru.wikipedia.org
```

### HTTP response (на запрос на страничку про HTTP на wikipedia.org)

```
HTTP/1.0 200 OK
<payload>
```

### HTTP Methods

Это некоторые операции над сервером. 

Каждый сервер обязан поддерживать как минимум методы GET и HEAD. Если сервер не распознал указанный клиентом метод, то он должен вернуть статус 501 (Not Implemented). Если серверу метод известен, но он неприменим к конкретному ресурсу, то возвращается сообщение с кодом 405 (Method Not Allowed). В обоих случаях серверу следует включить в сообщение ответа заголовок Allow со списком поддерживаемых методов.
Кроме методов GET и HEAD, часто применяется метод POST.

* `GET` - запрос содержимого указанного ресурса, так же различают ещё условный GET (с заголовками If-Modified-Since, If-Match, If-Range, etc.) и частичный GET (с заголовком Range);
* `HEAD` - запрос метаинформации, проверка наличия ресурса, ответ сервера не содержит тело;
* `OPTIONS` - проверка списка поддерживаемых методов, "пингование" сервера, определение параметров сервера и соединения;
* `POST` - передача данных от клиента на сервер, предполагается что сервер будет обрабатывать загруженные данные;
* `PUT` - загрузка данных сервер, предполагается, что они будут доступным по адресу, на который производилась загрузка;
* `PATCH` - аналогично `PUT`, но для какого то фрагмента данных;
* `DELETE` - удаление указанного ресурса.

### HTTP Status codes

Код состояния является частью первой строки ответа сервера. Это целое трехзначное число. Первая цифра - класс состояния. Обычно за кодом через пробел следует фраза на английском языке, поясняющая состояние.

Основные состояния:

* `1xx Informational` - информирование о процессе передачи, например, `100 Continue`, `101 Switching Protocols`, etc;
* `2xx Success` - информирование об успешном принятии и обработке запроса от клиента, например, `200OK`, `201 Created`, etc;
* `3xx Redirection` - информирование о необходимость использовать другой адрес, например, сделать запрос на другой адрес;
* `4xx Client Error` - информирование об ошибке со стороны клиента, например, `403 Forbidden`, `404 Not Found`, `408 Request Timeout`, etc;
* `5xx Server Error` - информирование об ошибке со стороны сервера (во время обработки запроса), например, `500 Internal Server Error`, `501 Not Implemented`, etc;

Подробное описание - [wiki: List of HTTP status codes](http://en.wikipedia.org/wiki/List_of_HTTP_status_codes)

### HTTP Headers

Этоо строки в HTTP-сообщении, содержащие разделённую двоеточием пару параметр-значение. Заголовки должны отделяться от тела сообщения хотя бы одной пустой строкой.

Некоторые заголовки:

* `Server` - имя используемого веб-сервера, зачастую содержит информация об ОС и версиях;
* `Content-Type` - тип контента (`text/html`, `text/plain`, etc);

Подробное описание - [List of HTTP header fields](http://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

Множество примеров есть на [wikipedia](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol).

## Servlet API (version < 3.0)

Это просто набор интерфейсов, спецификация. Существует множество реализаций - сервлет-контейнеры (servlet container - Tomcat, Jetty, etc).
Это описание модели взаимодействие с HTTP протоколом в виде управления запросами (request) и ответами (response).

Веб-приложение (web application) представляет из себя WAR-файл специального вида.

Сервлет-контейнер может содержать в себе несколько веб-приложений.

Все общение с окружающим миром реализуется через интерфейсы сервлет-контейнера.

### Web application structure

По спецификации Java веб-приложения упаковываются в war архивы (Web ARchive), являющийся тем же самым jar'ом со специальной структурой. Обычно, когда application container нфходит war-файл, он деплоит его и он становится доступным по некоторому адресу.

Вниутри war'a необходима только одна директория - `WEB-INF`. Содержимое этой директории недоступно пользователям веб-сервера напрямую. Эта директория содержит директории `classes` и `lib` со скомпилированными классами нашего приложения и дополнителными jar'aми (библиотеками) соответственно. Содержимое этих папок будет автоматически загружено class loader'ом веб-приложения. Так же в папке `WEB-INF` должен быть файл `web.xml`, содержащий deployment description. Если приложение использует только JSP, то этот файл необязателен. В нем содержится описание всех сервлетов, фильтров и тд.

### Servlet Life Cycle

* loading;
* instantiation;
* initialization;
* serving;
* destroying.

### `javax.servlet.http.HttpServlet`

```java
public abstract class HttpServlet extends javax.servlet.GenericServlet {

	public void init(ServletConfig config) throws ServletException { }

	public void init() throws ServletException { }
    
    protected void do<Get/Head/Post/Put/Delete/Options/Trace>(javax.servlet.http.HttpServletRequest req, javax.servlet.http.HttpServletResponse resp) throws javax.servlet.ServletException, java.io.IOException { }

    protected void service(javax.servlet.http.HttpServletRequest req, javax.servlet.http.HttpServletResponse resp) throws javax.servlet.ServletException, java.io.IOException { }

    public void destroy() { }

}
```

### Servlets Deployment Description

```xml
<servlet>
	<description>This is a servlet</description>
	<display-name>Servlet #1</display-name>
	<servlet-name>FirstServlet</servlet-name>
	<class>ru.frostman.FirstServlet</class>
	<init-param>
		<param-name>some-arg</param-name>
		<param-value>some-value</param-value>
	</init-param>
</servlet>

<servlet-mapping>
	<servlet-name>FirstServlet</servlet-name>
	<uri-pattern>/path/to/first/servlet</uti-pattern>
</servlet-mapping>
```

### `javax.servlet.Filter`

```java
public interface Filter {
    void init(javax.servlet.FilterConfig filterConfig) throws javax.servlet.ServletException;

    void doFilter(javax.servlet.ServletRequest servletRequest, javax.servlet.ServletResponse servletResponse, javax.servlet.FilterChain filterChain) throws java.io.IOException, javax.servlet.ServletException;

    void destroy();
}
```

Они так же описываются в web.xml

Filter chains.

### Listeners

* `ServletContextListener`;
* `HttpSessionListener`;
* etc.

## Servlet API (version >= 3.0)

Цели: 

* упрощение разработки;
* модульность;
* асинхронная обработка;
* управление безопасностью;
* динамическая регистрация сервлетов и фильтров

### Annotations (`javax.servlet.annotation`)

web.xml теперь необязателен, сервлет-контейнер отвечает за обработку аннотированных классов расположенных в каталоге WEB-INF/classes, в архивах из каталога WEB-INF/lib, или где-либо еще в classpath. Приложение в jar'e тоже может содержать перечисленные элементы. 

* `@WebServlet` - помеченный этой аннотацией класс является сервлетом (так же он должен быть унаследован от HttpServlet);
* `@WebFilter` - тоже самое, но для фильтров;
* `@WebListener` - помеченный этой аннотацией listener, будет подгружен сервлет-контейнером в качестве обработчика событий для данного веб-приложения.

### Dynamic servlet loading

```java
@WebListener
public class TestListener implements ServletContextListener {
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        ServletContext servletContext = servletContextEvent.getServletContext();

        String className = "ru.frostman.DynamicServlet";
        String servletName = "DynamicServlet";
        String servletRequestPattern = "/dynamic";

        ServletRegistration.Dynamic dynamic = servletContext.addServlet(servletName, className);
        dynamic.addMapping(servletRequestPattern);
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
    }
}
```

### Maven

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
</dependency>
```

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.0.1</version>
</dependency>
```

## JSP

```
<p>Counting to three:</p>
<% for (int i = 1; i < 4; i++) { %>
    <p>This number is <%= i %>.</p>
<% } %>
<p>Done counting.</p>
```

## Практической задание (сделать на занятии)

Что то очень простое, типа пара страничек + фильтр
Странички просто в webapp, на след занятии - шаблонизаторы
 
## Домашнее задание

### Задание #1

Что то по типу файлового сервера с базой пользователей (см на вики задание с прошлого года)
