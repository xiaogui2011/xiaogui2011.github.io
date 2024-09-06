---
layout: post
title: golang&kafka
slug: golang&kafka
author: ymkNK
date: 2024-07-25 00:09:24 +0800
categories: [开发, golang]
tags: [goroutine， golang]
img: 1.jpg
---


## 关闭consumer连接

kafka server: Tried to send a message to a replica that is not the leader for some partition. Your metadata is out of date

在每次重试之前，调用 consumer.Consumer.Close() 方法关闭现有的消费者，然后重新创建一个新的消费者。这可以确保消费者重新获取最新的元数据。


```golang
func NewPartitionConsumer(kfkConfig config.Kafka, topic string, partition int32) (sarama.PartitionConsumer, error) {
	var consumer *kafka.Consumer
	var err error
	var consumerConfig kafka.KafkaConfig

	if len(kfkConfig.ZKHost) != 0 {
		logger.Debug(kfkConfig.ZKHost, kfkConfig.ZKAuth)
		hosts, err := kafka.GetKafkaHosts(kfkConfig.ZKHost, kfkConfig.ZKAuth)
		if err != nil {
			logger.Error(err.Error())
			return nil, err
		}
		logger.Debug(hosts)
		consumer, err = kafka.NewKafkaConsumerByZookeeper(hosts)
		if err != nil {
			logger.Error(err.Error())
			return nil, err
		}
	} else {
		consumerConfig.Hosts = kfkConfig.KfkHost
		consumerConfig.Auth = kfkConfig.KfkAuth
		consumerConfig.Mechanism = kfkConfig.KfkMechanism
		consumerConfig.SASLEnable = kfkConfig.KfkSaslEnable
		consumerConfig.ClientId = kfkConfig.ClientID
		consumer, err = kafka.NewKafkaConsumer(consumerConfig)
		if err != nil {
			logger.Error(err.Error(), consumerConfig)
			return nil, err
		}
	}

	// 尝试多次重试
	maxRetries := 5
	retryInterval := time.Second * 2 // 重试间隔时间

	for i := 0; i < maxRetries; i++ {
		logger.Debug(topic, partition)
		partitionConsumer, err := consumer.Consumer.ConsumePartition(topic, partition, sarama.OffsetNewest)
		if err != nil {
			// 尝试重试
			logger.Error("Consume partition failed, retrying:", err)

			// 关闭现有消费者
			if err := consumer.Consumer.Close(); err != nil {
				logger.Error("Failed to close consumer:", err)
				return nil, err
			}

			// 重新创建消费者
			if len(kfkConfig.ZKHost) != 0 {
				consumer, err = kafka.NewKafkaConsumerByZookeeper(kfkConfig.ZKHost)
				if err != nil {
					logger.Error(err.Error())
					return nil, err
				}
			} else {
				consumer, err = kafka.NewKafkaConsumer(consumerConfig)
				if err != nil {
					logger.Error(err.Error(), consumerConfig)
					return nil, err
				}
			}

			// 等待一段时间后重试
			time.Sleep(retryInterval)
		} else {
			return partitionConsumer, nil
		}
	}

	return nil, fmt.Errorf("failed to consume partition after %d retries", maxRetries)
}
```

[//]: # (TODO 添加kafka消费者组)
