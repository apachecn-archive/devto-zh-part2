# 游戏的生活与反应原生易网格

> 原文：<https://dev.to/kmukabe/game-of-life-with-react-native-easygrid-54ba>

在过去的一两个月里，我一直在学习 react native，我决定通过做一些有趣的小项目来学习和熟悉它。在我的第一个项目中，我使用 react native easy grid 和 create-react-native-app 创建了康威的《生活的游戏》。首先创建一个 CRNA 应用程序并安装网格库。
create-react-native-app gameo life&T5】CD gameo life
NPM 安装 react-native-easy-grid
然后在 App.js 中导入 easy grid 并创建以下类:

```
import React from 'react';
import { StyleSheet, View} from 'react-native';
import {Col, Row, Grid} from 'react-native-easy-grid';
export default class App extends React.Component {
  gridNum = 40; // The number of rows and columns in our grid
  constructor(props){
    super(props);
    let newBoard = this.setupBoard() 
    this.state = {
      board: newBoard
    }
  }
  setupBoard = () => {
    // Create a two dimensional array to represent the grid
    let board = new Array(this.gridNum)
    for (i = 0; i < this.gridNum; i++){
      board[i] = new Array(this.gridNum)
    }
    for(x=0; x< this.gridNum; x++){
      for(y=0; y < this.gridNum; y++){
        // Creates a grid square object that holds a random number 
        // between 0 for a dead cell and 1 for alive 
        // n represents the number of neighbours a cell has
        board[x][y] = {
          cell:Math.floor(Math.random()*2),
          n:0
        }
      }
    }
    return board
  }

  render(){
    return(
      <Grid>
        // Code To be Done
      </Grid>
    );
  }
} 
```

上面的代码设置了棋盘，然后我创建了两个函数:
检查每个单元格的邻居数量，
函数获取下一代单元格。

```
checkNeighbours = () => {
  let oldBoard = this.state.board
  // Ignores the columns and rows on the outermost edges
  for(x=1; x< this.gridNum-1; x++){
    for(y=1; y < this.gridNum-1; y++){
      \\ For every grid cell check how many neighbours it has
      for(i=-1; i< 2; i++){
        for(j=-1; j < 2; j++){
          oldBoard[x][y].n += oldBoard[(x+i)][(y+j)].cell
        }
      }
      \\ Delete the value of the current cell as it was counted in  
      \\ the above calculation
      oldBoard[x][y].n -= oldBoard[x][y].cell
    }
  }
  this.setState({board: oldBoard})
}
newBoard = () => {
  let oldBoard = this.state.board
  let board = this.setupBoard()
  // Set all new board values to zero
  for(let a = 0; a < this.gridNum; a++){
    for(let b=0; b<this.gridNum;b++){
      board[a][b] = {cell:0, n:0}
    }
  }
  for(x=1; x< this.gridNum-1; x++){
    for(y=1; y < this.gridNum-1; y++){
      if((oldBoard[x][y].cell == 1) && oldBoard[x][y].n < 2){
        board[x][y].cell = 0
      } else if((oldBoard[x][y].cell == 1) && oldBoard[x][y].n > 3){
        board[x][y].cell = 0
      } else if((oldBoard[x][y].cell == 0) && oldBoard[x][y].n == 3)
      {
        board[x][y].cell = 1
      } else if((oldBoard[x][y].cell == 1)&& (oldBoard[x][y].n == 3 
                 || oldBoard[x][y].n == 2)){
        board[x][y].cell = 1
      } else{
        board[x][y].cell = oldBoard[x][y].cell
      }
    }
  }
  this.setState({board: board})
}
For the game of life to update these two functions above must be called continuously. For this I created a helper function called "run" and used setInterval() to have it called every second.
constructor(props){
  ... // Above code skipped for brevity
  setInterval(this.run, 1000);
}
run = () => {
  this.checkNeighbours()
  this.newBoard()
} 
```

最后，我将网格渲染到屏幕上。

```
render(){
  return(
    <Grid>
      {this.state.board.map((row, key)=>{
        return <Col key={key}>{row.map((cell, key)=>{
          return <Row style= 
            // If cell is 1(alive) render a color there
            {{backgroundColor:cell.cell==1?'#00FFFF':'#FFF', 
            margin:1}} key={key}></Row>
        })}</Col>
      })}
    </Grid>
  );
} 
```

我知道在 three.js 中这样做可能更优雅，但这是一个有趣的实验。这里有一个到 Github 库的链接。以及最终产品(已经稍微加快了速度)。

[![Game of Life react native app gif](img/c026a0c09674428cbdcee3bb7a5a3954.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---CV5WzJI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v2i95vkxjq5m83j0bhol.gif)