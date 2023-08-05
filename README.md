# Домашнее задание к занятию "Что такое DevOps. СI/СD" - Мильдзихов Сергей

### Задание 1

**Что нужно сделать:**

1.Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно.

2. Установите на машину с jenkins [golang](https://golang.org/doc/install).

3. Используя свой аккаунт на GitHub, сделайте себе форк [репозитория](https://github.com/netology-code/sdvps-materials.git). В этом же репозитории находится [дополнительный материал для выполнения ДЗ](https://github.com/netology-code/sdvps-materials/blob/main/CICD/8.2-hw.md).

4. Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта ```go test .``` и  ```docker build .```.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

---

**Выполнение задания:**



***настройки***

https://github.com/mildzikhov01/DZ_8_03.git
![screen1](https://github.com/mildzikhov01/DZ_8_03/blob/main/DZ_Git_Settings_3.png)

![screen2](https://github.com/mildzikhov01/DZ_8_03/blob/main/DZ_Git_Settings_4.png)


***результат сборки***

![screen6](https://github.com/mildzikhov01/DZ_8_03/blob/main/Screenshot_1.png)


---

### Задание 2


**Что нужно сделать:**

1. Создайте новый проект pipeline.
2. Перепишите сборку из задания 1 на declarative в виде кода.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

--

**Выполнение задания:**

проект pipeline

```
pipeline {
 agent any
 stages {
  stage('Git') {
   steps {git 'https://github.com/mildzikhov01/DZ_DevOps_CI_CD.git'}
  }
  stage('Test') {
   steps {
    sh 'go test .'
   }
  }
  stage('Build') {
   steps {
    sh 'docker build . -t ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER'
   }
  }
  stage('Push') {
   steps {
    sh 'docker login ubuntu-bionic:8082 -u admin -p admin && docker push ubuntu-bionic:8082/hello-world:v$BUILD_NUMBER && docker logout'   }
  }
 }
}
```

скриншот выполенения сборки

![screen](https://github.com/mildzikhov01/DZ_8_03/blob/main/Screenshot_pipeline.jpg)


---

### Задание 3


**Что нужно сделать:**

1. Установите на машину Nexus.
2. Создайте raw-hosted репозиторий.
3. Измените pipeline так, чтобы вместо Docker-образа собирался бинарный go-файл. Команду можно скопировать из Dockerfile.
4. Загрузите файл в репозиторий с помощью jenkins.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

--

**Выполнение задания:**

pipeline
```
pipeline {
 agent any
 stages {
  stage('Git') {
   steps {git 'https://github.com/mildzikhov01/DZ_DevOps_CI_CD.git'}
  }
  stage('Test') {
   steps {
    sh 'go test .'
   }
  }
  stage('Build') {
   steps {
    sh '/usr/local/go/bin/go build -a -installsuffix nocgo -o ./app'
     }
  }
  stage('Push') {
   steps {
    sh 'curl -u admin:admin http://192.168.10.22:8081/repository/rh-8-03-hw/ --upload-file ./app'   
    }
  }
 }
}
```

Результат выполения сборки и отправки файла в репозиторий

![screen](https://github.com/mildzikhov01/DZ_8_03/blob/main/jenkins_build_go.JPG)

Результат загрузки файла в репозиторий

![screen](https://github.com/mildzikhov01/DZ_8_03/blob/main/nexus_repo-rh-8-03-hw.JPG)

