# Лабораторная работа №3  
## Оркестрация контейнеров в Kubernetes  

**ФИО:** Попов Александр Евгеньевич 
**Группа:** АДЭУ-221 
**Вариант:** 10  

---

## Цель работы  
Развернуть Elasticsearch и Kibana в Kubernetes, настроить их взаимодействие и проверить работоспособность системы.

---

## Ход выполнения  

### 1. Подготовка манифестов  

На первом этапе были созданы YAML-манифесты для развертывания Elasticsearch и Kibana, а также соответствующих сервисов.

<img width="690" height="448" alt="image-45" src="https://github.com/user-attachments/assets/9f97704d-8e6c-4095-9f9a-ab61a2eca556" />


<img width="681" height="444" alt="image-46" src="https://github.com/user-attachments/assets/3413b355-7776-4e79-bb36-e3c6b949024a" />


<img width="689" height="443" alt="image-47" src="https://github.com/user-attachments/assets/1390161f-24fb-4ff2-9fce-d5c01f80c931" />


<img width="690" height="440" alt="image-48" src="https://github.com/user-attachments/assets/e2d29652-0768-4255-81e2-c1703864d1b4" />

---

### 2. Запуск системы  

Для развертывания всех компонентов была выполнена команда:

```
bash
kubectl apply -f .
```

### 3. Проверка состояния кластера  
В начале работы была выполнена проверка Kubernetes-кластера с помощью следующих команд: 
```
kubectl get nodes  
kubectl get pods -A  
```

Кластер находится в рабочем состоянии, узел имеет статус Ready.  


---

### 4. Запуск манифестов  
Для развертывания Elasticsearch и Kibana были применены YAML-манифесты командой:  
```
kubectl apply -f .  
```

---

### 5. Проверка pod'ов  
После запуска была выполнена проверка pod'ов:  
```
kubectl get pods  
```
<img width="506" height="78" alt="image-49" src="https://github.com/user-attachments/assets/eaeadea1-1bed-415b-9482-b947fcc53f9a" />

Все pod'ы успешно перешли в состояние Running, что подтверждает корректный запуск контейнеров.  


---

### 6. Проверка сервисов  
Для проверки сетевого доступа были выведены сервисы:  
```
kubectl get services  
```
Были созданы сервис для Elasticsearch (es-service) и сервис для Kibana (kibana-service типа NodePort).  


---

### 7. Проверка работы Elasticsearch  
Для проверки работы Elasticsearch выполнен проброс порта:  
kubectl port-forward svc/es-service 9200:9200  
После этого во втором терминале выполнен запрос:  
curl http://localhost:9200  
Получен JSON-ответ от Elasticsearch, что подтверждает его работоспособность.  

<img width="1174" height="716" alt="image-52" src="https://github.com/user-attachments/assets/c063464e-5cf2-4fc4-8eaa-85074fcf72ec" />

---

### 8. Проверка Kibana  
Веб-интерфейс Kibana был открыт в браузере по адресу:  
http://localhost:30008  
Интерфейс успешно загрузился, что подтверждает корректную работу связки Elasticsearch и Kibana.  

<img width="1469" height="832" alt="image-51" src="https://github.com/user-attachments/assets/c21ef1d2-a379-4278-a83d-d30aa250de96" />

---

## Ошибка и её решение  

### Ошибка  
В процессе выполнения работы Kibana не запускалась и отображала сообщение:  
Kibana server is not ready yet  
В логах присутствовала ошибка:  
Unable to retrieve version information from Elasticsearch nodes  

<img width="1178" height="725" alt="image-50" src="https://github.com/user-attachments/assets/fa1126a8-2b9e-490d-bc94-bd33807c3b03" />

---

### Причина  
Причиной являлось то, что в Elasticsearch версии 8 по умолчанию включена система безопасности (xpack security), которая требует авторизации и препятствует подключению Kibana.

---

### Решение  
Для устранения ошибки в файл es-deployment.yaml были добавлены переменные окружения:  
xpack.security.enabled=false  
ES_JAVA_OPTS="-Xms256m -Xmx256m"  
После этого pod'ы были перезапущены командой:  
kubectl delete pod <имя-пода>  
После перезапуска Kibana успешно подключилась к Elasticsearch.

---

## Вывод  
В ходе выполнения лабораторной работы были успешно развернуты Elasticsearch и Kibana в Kubernetes, настроено их взаимодействие через Service, обеспечен доступ к системе через NodePort, а также выполнена проверка работоспособности с помощью curl и браузера. В процессе работы была выявлена и устранена ошибка, связанная с настройками безопасности Elasticsearch. Kubernetes позволяет эффективно управлять контейнерными приложениями и их сетевым взаимодействием.

---

## Структура проекта  
lab_03_kibana/  
├── es-deployment.yaml  
├── es-service.yaml  
├── kibana-deployment.yaml  
├── kibana-service.yaml  
└── README.md  
