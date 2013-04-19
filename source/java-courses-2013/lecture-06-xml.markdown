---                                                                                                                     
layout: page                                                                                                            
title: JC13 - Lecture 06. XML parsing/generation                                                                                                                 
comments: false                                                                                                         
sharing: false                                                                                                          
sidebar: collapse
footer: false                                                                                                           
---
## [Mirantis](http://www.mirantis.com) Java Сourse 2013 ([back](index.html))
## Lecture 06. XML parsing/generation.

В Java существует 2 типа XML парсеров:

1. DOM парсер - обрабатывает xml файл целиком и строит полное DOM дерево для него;
2. SAX парсер - обрабатывает xml файл небольшими кусочками и вызывает определенные пользователем хендлеры на различные события, такие как:
	a. начало документа;
	b. конец документа;
	c. открывающий тег;
	d. закрывающий тэг;
	e. текст (между тегов).
	
Генерировать XML можно при помощи объекта `DocumentBuilder`.

Пример генерации xml:

```java
        DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
        DocumentBuilder builder;
        try {
            builder = factory.newDocumentBuilder();
        } catch (ParserConfigurationException e) {
            throw new ParseException("Error while creating DocumentBuilder.", e);
        }

        Document document = builder.newDocument();

        Node departmentRoot = document.createElement("department");
        document.appendChild(departmentRoot);

        Element deanElement = document.createElement("dean");
        deanElement.setAttribute("name", department.getDean().getName());
        deanElement.setAttribute("since", String.valueOf(department.getDean().getSince()));
        departmentRoot.appendChild(deanElement);

        Element studentsElement = document.createElement("students");
        departmentRoot.appendChild(studentsElement);

        Map<Student, List<Group>> studentGroups = new HashMap<Student, List<Group>>();

        for (Group group : department.getGroups()) {
            for (Student student : group.getStudents()) {
                if (!studentGroups.containsKey(student)) {
                    studentGroups.put(student, new ArrayList<Group>());
                }
                studentGroups.get(student).add(group);
            }
        }

        for (Map.Entry<Student, List<Group>> studentEntry : studentGroups.entrySet()) {
            Element studentElement = document.createElement("student");
            studentElement.setAttribute("name", studentEntry.getKey().getName());
            studentElement.setAttribute("excellent", String.valueOf(studentEntry.getKey().isExcellent()));
            studentsElement.appendChild(studentElement);

            Element groupsElement = document.createElement("groups");
            studentElement.appendChild(groupsElement);
            for (Group group : studentEntry.getValue()) {
                Node groupElement = document.createElement("group");
                //
                groupElement.setTextContent(String.valueOf(group.getNumber()));
                groupsElement.appendChild(groupElement);

            }
        }

        // вывод отформатированного xml
        TransformerFactory tFactory = TransformerFactory.newInstance();

        Transformer transformer = null;
        try {
            transformer = tFactory.newTransformer();
        } catch (TransformerConfigurationException e) {
            throw new ParseException("Error while creating DOM transformer", e);
        }

        transformer.setOutputProperty("{http://xml.apache.org/xslt}indent-amount", "4");
        transformer.setOutputProperty(OutputKeys.INDENT, "yes");

        DOMSource source = new DOMSource(document);
        StreamResult result = new StreamResult(file);
        try {
            transformer.transform(source, result);
        } catch (TransformerException e) {
            throw new ParseException("Error while transforming DOM document", e);
        }
    }
```


## Практическое задание (на занятии) #1 / Домашнее задание

Необходимо реализовать DOM и SAX парсер следующего xml файла, который будет инициализовать модель, состоящую из следующих классов - `Departments`, `Department`, `Employee`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<departmants>
    <department name="department 1">
        <lead since="2007">
            Ivan Ivanov
        </lead>
        <employees>
            <employee since="2012">
                Sergey Sergeev
            </employee>
            <employee since="2010">
                Ivan Ivanov_2
            </employee>
            ...
        </employees>
    </department>
    ...
</departmants>
```

Так же по полученной модели необходимо сгенерировать xml файл следующего формата:

```xml
<persons>
    <person name="Ivan Ivanov">
        <departments>
            <department name="department 1" since="2007">
                lead
            </department>
            ...
        </departments>
    </person>
    ...
</persons>
```

