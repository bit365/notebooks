
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

rabbitmq rabbitmqctl enable_feature_flag all
rabbitmq rabbitmqctl disable_feature_flag all
```

# Rabbitmq Auth Backend Http


[RabbitMQ server to perform authentication](https://github.com/rabbitmq/rabbitmq-server/tree/main/deps/rabbitmq_auth_backend_http)

