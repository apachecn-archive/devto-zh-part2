# 初级节点帮助-第 2 课

> 原文：<https://dev.to/masmithrpg/beginner-node-help--take-2-14pg>

我对 HTML，NODE，PUG 等都很陌生。我正在尝试构建一个高尔夫

分数应用程序。我已经创建了允许我
CRUD 课程的第一部分。这部分工作正常。
我有 2 张表，课程和分数。

在显示了一个球场列表后，我选择了一个球场并加载了 6 个空白的 HTML 表格记录，以允许输入高尔夫球手并对 18 个球洞中的每个球洞进行评分
。现在正是我困惑的地方，需要一些指导。

```
 in my .pug file, how do i display an output field that displays a 
         running total of my score?

         On my submit button, i'm not getting to my POST, but i cannot figure 
         out why

         I want to be able to hit save on this screen and either Insert or 
         Update, depending on whether i have already saved.  Should that be 
         handled within a single POST?  It seems like i should, but i don't 
         know enough about Node to know

         I am processing my courses and scores within a single index.js file.  
         Should i be separating the courses/holes into separate .js files?

         Thats enough for now
         Thanks for any guidance you can provide

        app.get('/scores/keepscore', function(req, res) { 
```

res.render('scores/keepscore '，{ title:'记分卡'，form _ action:`/scores/update`})
})
app . post('/scores/update '，function(req，res) {
console.log('我到了更新')
let stmt = new db . db stmt(db conn)
console . log(req)
var SQL =
`INSERT INTO ${schema}.SCORECARD (COURSE,GOLFER, GDATE, HOLE1,HOLE2,HOLE3,HOLE4, HOLE5, HOLE6, HOLE7, HOLE8, HOLE9, HOLE10, HOLE11, HOLE12, HOLE13, HOLE14, HOLE15, HOLE16, HOLE17, HOLE18)
VALUES ('${req.body.COURSE}','${req.body.GOLFER}',CURRENT_DATE, ${req.body.HOLER1}, ${req.body.HOLE2}, ${req.body.HOLE3},${req.body.HOLE4}, ${req.body.HOLE5}, ${req.body.HOLE6},
${req.body.HOLE7}, ${req.body.HOLE8}, ${req.body.HOLE9},${req.body.HOLE10}, ${req.body.HOLE11}, ${req.body.HOLE12},${req.body.HOLE13}, ${req.body.HOLE14}, ${req.body.HOLE15},
${req.body.HOLE16}, ${req.body.HOLE17}, ${req.body.HOLE18} ) with NC`
stmt . exec(SQL，function(result，err){
console。