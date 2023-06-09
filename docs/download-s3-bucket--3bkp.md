# 下载 S3 桶

> 原文：<https://dev.to/neo01124/download-s3-bucket--3bkp>

*原载于[https://gist . github . com/neo 01124/DC 31d 0 b 08 BD 7 AC 6906d 06197 e 20 DC 9 b 6](https://gist.github.com/neo01124/dc31d0b08bd7ac6906d06197e20dc9b6)T3】*

这一定是我第五次为不同的项目写这种代码，并决定永远记下来。

这可能看起来是一个非常琐碎的任务，直到你意识到 S3 没有文件夹层次的概念。S3 只有水桶和钥匙的概念。桶是平的，即没有文件夹。整个路径(folder 1/folder 2/folder 3/file . txt)是对象的关键字。S3 用户界面就像一个文件浏览器，但没有任何文件夹。桶里只有钥匙。来自 [S3 的文件](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html)

> 亚马逊 S3 数据模型是一个平面结构:您创建一个 bucket，bucket >存储对象。子容器或子文件夹没有层次结构；但是，您可以像亚马逊> S3 控制台一样，使用键名前缀和分隔符来推断逻辑层次。

这项任务的挑战是在下载 S3 对象的实际内容之前，在密钥中创建目录结构(文件夹 1/文件夹 2/文件夹 3/)。

# 选项 1 - Shell 命令

Aws cli 将通过[同步操作](https://docs.aws.amazon.com/cli/latest/reference/s3/sync.html)
为您完成此操作

```
aws s3 sync s3://yourbucket /local/path 
```

Enter fullscreen mode Exit fullscreen mode

# 选项 2 - Python

*   安装 [boto3](http://boto3.readthedocs.io/en/latest/guide/quickstart.html#installation)
*   使用类似的策略创建 IAM 用户

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "VisualEditor0",  "Effect":  "Allow",  "Action":  [  "s3:ListBucket",  "s3:ListBucketMultipartUploads",  "s3:ListMultipartUploadParts",  "s3:GetObject",  "s3:GetBucketLocation",  ],  "Resource":  [  "arn:aws:s3:::your_bucket_name"  ]  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

*   在~/中创建描述文件。AWS/凭证以及该 IAM 用户的访问详细信息，如 [boto 文档](http://boto3.readthedocs.io/en/latest/guide/configuration.html#shared-credentials-file)中所述
*   密码

```
import boto3, errno, os

def mkdir_p(path):
    # mkdir -p functionality from https://stackoverflow.com/a/600612/2448314
    try:
        os.makedirs(path)
    except OSError as exc:  # Python >2.5
        if exc.errno == errno.EEXIST and os.path.isdir(path):
            pass
        else:
            raise

def get_s3_path_filename(key):
    key = str(key)
    return key.replace(key.split('/')[-1],""),  key.split('/')[-1]

def download_s3_bucket(bucket_name, local_folder, aws_user_with_s3_access):
    session = boto3.Session(profile_name=aws_user_with_s3_access)
    s3_client = session.resource('s3')
    s3_bucket = s3_client.Bucket(bucket_name)
    for obj in s3_bucket.objects.all():
        s3_path, s3_filename = get_s3_path_filename(obj.key)
        local_folder_path = os.path.join(*[os.curdir,local_folder, s3_path])
        local_fullpath = os.path.join(*[local_folder_path, s3_filename])
        mkdir_p(local_folder_path)
        s3_bucket.download_file(obj.key, local_fullpath)

download_s3_bucket(bucket_name = your_bucket_name, local_folder = "/tmp/s3_bucket", aws_user_with_s3_access = profile_name) 
```

Enter fullscreen mode Exit fullscreen mode

如果有足够的兴趣，我会制作一个包:)