# 在 Golang 使用 AWS Lambda +弹性转码器进行视频处理

> 原文：<https://dev.to/mmyoji/video-processing-with-aws-lambda--elastic-transcoder-in-golang--hf2>

我发这个帖子是因为我在 Golang 中找不到任何带有 AWS Lambda + AWS Elastic Transcoder 视频处理示例代码的博客帖子。

## 依赖关系

*   Golang v1.10
*   aws/aws-lambda-go 版本 1.1.0
*   aws/aws-sdk-go v1.13.8

## 处所

*   你大致知道 [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/welcome.html) 和[弹性转码器](https://docs.aws.amazon.com/elastictranscoder/latest/developerguide/introduction.html)
*   你已经有了一个[弹性代码转换器管道](https://docs.aws.amazon.com/elastictranscoder/latest/developerguide/working-with-pipelines.html)
*   你了解 Golang 一点，喜欢:)

## 操作的流程

1.  在 S3 桶中上传视频文件
2.  λ由 1 触发。并创建一个[弹性转码器作业](https://docs.aws.amazon.com/elastictranscoder/latest/developerguide/working-with-jobs.html)

就是这样。

## 实现

创建 Lambda 函数

*   运行时:`Go 1.x`
*   设置适当的角色(需要弹性代码转换器访问)
*   在`go build`后设置一个与 Go 二进制相同的处理程序名
*   设置触发器为`S3`并选择管道的输入桶名称和事件类型`ObjectCreated`
*   设置您的`PIPELINE_ID`环境变量

Go 代码

```
package main

import (
    "context"
    "fmt"
    "log"
    "os"

    "github.com/aws/aws-lambda-go/events"
    "github.com/aws/aws-lambda-go/lambda"
    "github.com/aws/aws-sdk-go/aws"
    "github.com/aws/aws-sdk-go/aws/session"
    transcoder "github.com/aws/aws-sdk-go/service/elastictranscoder"
)

var (
    // Generic 720p
    presetID = "1351620000001-000010"
)

func main() {
    lambda.Start(handler)
}

func handler(ctx context.Context, s3Event events.S3Event) {
    for _, record := range s3Event.Records {
        s3 := record.S3
        bucket := s3.Bucket.Name
        objKey := s3.Object.Key
        log.Printf("[%s - %s] Bucket = %s, Key = %s \n", record.EventSource, record.EventTime, bucket, objKey)

        sess := session.Must(
            session.NewSession(
                &aws.Config{
                    // Set supported region
                    Region: aws.String("ap-northeast-1"),
                },
            ),
        )

        svc := transcoder.New(sess)

        resp, err := svc.CreateJob(
            &transcoder.CreateJobInput{
                Input: &transcoder.JobInput{
                    Key: aws.String(objKey),
                },
                Outputs: []*transcoder.CreateJobOutput{
                    &transcoder.CreateJobOutput{
                        PresetId:         aws.String(presetID),
                        Key:              aws.String(objKey),
                        // If the original filename is "test-file.mp4",
                        // this generates "test-file.mp4-00001.png"
                        ThumbnailPattern: aws.String(fmt.Sprintf("%s-{count}", objKey)),
                    },
                },
                PipelineId: aws.String(os.Getenv("PIPELINE_ID")),
            },
        )
        if err != nil {
            log.Printf("Failed: Create Job, %v\n", err)
            return
        }

        log.Printf("Job Response: %v\n", resp.Job)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

structs 选项是最少的，但是如果您不想生成缩略图，您可以将`CreateJobOutput.ThumbnailPattern`设置为`""`。

`ThumbnailPattern`必须包含`{count}`。

`aws.String(str)`与`&str`相同，你可以用这个作为速记。

```
strPtr := aws.String("somestring")

// the same with above, but you need to set a variable first.
str := "somestring"
strPtr := &str 
```

Enter fullscreen mode Exit fullscreen mode

## 参考文献

*   [elastictranscoder -亚马逊网络服务- Go SDK](https://docs.aws.amazon.com/sdk-for-go/api/service/elastictranscoder/)
*   [创建弹性转码器作业时指定的设置-亚马逊弹性转码器](https://docs.aws.amazon.com/elastictranscoder/latest/developerguide/job-settings.html)
*   [系统预置-亚马逊弹性转码器](https://docs.aws.amazon.com/elastictranscoder/latest/developerguide/system-presets.html)
*   [尝试用亚马逊电子传输制作了缩略图图像| Developers.IO](https://dev.classmethod.jp/cloud/aws/amazon-elastic-transcoder-thumbnail/) ( JP )