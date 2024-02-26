
# RabbitMQ Management

```
rabbitmq-plugins enable rabbitmq_management
```

```
Default User：guest/guest
Add User：test/test
```

[http://192.168.0.202:15672](http://192.168.0.202:15672)

[http://mqtt.yuangan:15672](http://mqtt.yuangan:15672)

# RabbitMQ Management MQTT

```
rabbitmq-plugins enable rabbitmq_mqtt
rabbitmq-plugins disable rabbitmq_mqtt

rabbitmq-plugins enable rabbitmq_web_mqtt
rabbitmq-plugins disable rabbitmq_web_mqtt

rabbitmq-plugins enable_feature_flag all
rabbitmq-plugins disable_feature_flag all
```

# Rabbitmq Auth Backend Http

```
rabbitmq-plugins enable rabbitmq_auth_backend_http
```

修改 rabbitmq.conf 配置文件，位置 %AppData%\RabbitMQ

```
auth_backends.1 = http
auth_http.http_method = post
auth_http.user_path = http://some-server/api/RabbitMqAuth/User
auth_http.vhost_path = http://some-server/api/RabbitMqAuth/Vhost
auth_http.resource_path = http://some-server/api/RabbitMqAuth/Resource
auth_http.topic_path = http://some-server/api/RabbitMqAuth/Topic
```

[RabbitMQ server to perform authentication](https://github.com/rabbitmq/rabbitmq-server/tree/main/deps/rabbitmq_auth_backend_http)

