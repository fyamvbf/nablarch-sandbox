nablarch-container-batchのアーキタイプから自動生成されたプロジェクトmyapp-container-batchを土台とし、
AWSのECS上で動作させるための調整をしたが、ソース自体は何も変わらなかった。（つまり、そのままAWS上で動作することの確認ができた。）

## AWSへの登録方法（概要）
※XXXXXの部分（各自のID）とap-northeast-1の部分は自身の環境に合わせること

１．dockerイメージを作成
`mvn clean package jib:dockerBuild`

２．専用タグの付与
`docker tag myapp-container-batch:latest XXXXXX.dkr.ecr.ap-northeast-1.amazonaws.com/nablarch-conainer-batch-example:myapp-container-batch`

３．ログイン
`aws ecr get-login-password --region ap-northeast-1 | docker login --username AWS --password-stdin XXXXXX.dkr.ecr.ap-northeast-1.amazonaws.com`

４．ECRにPUSH
`docker push XXXXXX.dkr.ecr.ap-northeast-1.amazonaws.com/nablarch-conainer-batch-example:myapp-container-batch`

５．AWS上でECSのタスク定義として作成し、タスクとして実行する。

タスク定義設定例
```
{
  "ipcMode": null,
  "executionRoleArn": "arn:aws:iam::XXXXXX:role/ecsTaskExecutionRole",
  "containerDefinitions": [
    {
      "dnsSearchDomains": null,
      "environmentFiles": null,
      "logConfiguration": {
        "logDriver": "awslogs",
        "secretOptions": null,
        "options": {
          "awslogs-group": "/ecs/myapp-container-batch",
          "awslogs-region": "ap-northeast-1",
          "awslogs-stream-prefix": "ecs"
        }
      },
      "entryPoint": null,
      "portMappings": [],
      "command": [
        "-diConfig",
        "classpath:batch-boot.xml",
        "-requestPath",
        "SampleBatch",
        "-userId",
        "batch_user"
      ],
      "linuxParameters": null,
      "cpu": 0,
      "environment": [
        {
          "name": "NABLARCH_FILEPATHSETTING_BASEPATHSETTINGS_FORMAT",
          "value": "file:/var/nablarch/format"
        },
        {
          "name": "NABLARCH_FILEPATHSETTING_BASEPATHSETTINGS_INPUT",
          "value": "file:/var/nablarch/input"
        },
        {
          "name": "NABLARCH_FILEPATHSETTING_BASEPATHSETTINGS_OUTPUT",
          "value": "file:/var/nablarch/output"
        }
      ],
      "resourceRequirements": null,
      "ulimits": null,
      "dnsServers": null,
      "mountPoints": [
        {
          "readOnly": null,
          "containerPath": "/h2",
          "sourceVolume": "h2"
        },
        {
          "readOnly": false,
          "containerPath": "/var",
          "sourceVolume": "resource"
        }
      ],
      "workingDirectory": null,
      "secrets": null,
      "dockerSecurityOptions": null,
      "memory": null,
      "memoryReservation": null,
      "volumesFrom": [],
      "stopTimeout": null,
      "image": "XXXXXX.dkr.ecr.ap-northeast-1.amazonaws.com/nablarch-conainer-batch-example:myapp-container-batch",
      "startTimeout": null,
      "firelensConfiguration": null,
      "dependsOn": null,
      "disableNetworking": null,
      "interactive": null,
      "healthCheck": null,
      "essential": true,
      "links": null,
      "hostname": null,
      "extraHosts": null,
      "pseudoTerminal": null,
      "user": null,
      "readonlyRootFilesystem": null,
      "dockerLabels": null,
      "systemControls": null,
      "privileged": null,
      "name": "myapp-container-batch"
    }
  ],
  "placementConstraints": [],
  "memory": "2048",
  "taskRoleArn": null,
  "compatibilities": [
    "EC2",
    "FARGATE"
  ],
  "taskDefinitionArn": "arn:aws:ecs:ap-northeast-1:XXXXXX:task-definition/myapp-container-batch-On-Demand:1",
  "family": "myapp-container-batch-On-Demand",
  "requiresAttributes": [
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "com.amazonaws.ecs.capability.logging-driver.awslogs"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "ecs.capability.execution-role-awslogs"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "ecs.capability.efsAuth"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "com.amazonaws.ecs.capability.ecr-auth"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "com.amazonaws.ecs.capability.docker-remote-api.1.19"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "ecs.capability.efs"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "com.amazonaws.ecs.capability.docker-remote-api.1.25"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "ecs.capability.execution-role-ecr-pull"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "com.amazonaws.ecs.capability.docker-remote-api.1.18"
    },
    {
      "targetId": null,
      "targetType": null,
      "value": null,
      "name": "ecs.capability.task-eni"
    }
  ],
  "pidMode": null,
  "requiresCompatibilities": [
    "FARGATE"
  ],
  "networkMode": "awsvpc",
  "cpu": "1024",
  "revision": 1,
  "status": "ACTIVE",
  "inferenceAccelerators": null,
  "proxyConfiguration": null,
  "volumes": [
    {
      "fsxWindowsFileServerVolumeConfiguration": null,
      "efsVolumeConfiguration": {
        "transitEncryptionPort": null,
        "fileSystemId": "XXXXXXX",
        "authorizationConfig": {
          "iam": "DISABLED",
          "accessPointId": "fsap-XXXXXXX"
        },
        "transitEncryption": "ENABLED",
        "rootDirectory": "/"
      },
      "name": "h2",
      "host": null,
      "dockerVolumeConfiguration": null
    },
    {
      "fsxWindowsFileServerVolumeConfiguration": null,
      "efsVolumeConfiguration": {
        "transitEncryptionPort": null,
        "fileSystemId": "XXXXXXX",
        "authorizationConfig": {
          "iam": "DISABLED",
          "accessPointId": "fsap-XXXXXXX"
        },
        "transitEncryption": "ENABLED",
        "rootDirectory": "/"
      },
      "name": "resource",
      "host": null,
      "dockerVolumeConfiguration": null
    }
  ]
}
```
