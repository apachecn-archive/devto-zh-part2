# 以 0.0.0.0 身份运行 Flask 应用程序

> 原文：<https://dev.to/mu/run-flask-app-as-0000-4ig5>

从 docker 容器访问 Flask 应用程序

第一步
`export FLASK_APP=execute.py`

第二步
`echo $FLASK_APP`

第三步
`flask run --host=0.0.0.0`