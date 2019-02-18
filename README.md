# 基于AWS的蓝/绿部署

## 目录
- 摘要
- 介绍
  1. 蓝绿部署方法论
  2. 蓝/绿的优点
  3. 定义环境边界
- AWS中保障蓝/绿部署的工具与服务
  1. Amazon Route 53
  2. Elastic Load Balancing
  3. Auto Scaling
  4. AWS Elastic Beanstalk
  5. AWS OpsWorks
  6. AWS CloudFormation
  7. Amazon CloudWatch
- 方案
  1. 使用Amazon Route 53更新DNS路由
  2. 在弹性负载均衡器下游交换Auto Scaling组
  3. 更新Auto Scaling组启动配置
  4. 交换Elastic Beanstalk应用环境
  5. 在AWS OpsWorks中克隆堆栈并更新DNS
- 数据同步管理和schema更改的最佳实践
  1. schema更改与代码更改间的解耦
- 何时不推荐蓝/绿部署
- 总结
- 贡献者
- 附录