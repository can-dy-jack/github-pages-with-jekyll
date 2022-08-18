---
title: "LeetCode每日一题-1706. 球会落何处（2022-2-24）"
---

## 题目

用一个大小为 m x n 的二维网格 grid 表示一个箱子。你有 n 颗球。箱子的顶部和底部都是开着的。
箱子中的每个单元格都有一个对角线挡板，跨过单元格的两个角，可以将球导向左侧或者右侧。

- 将球导向右侧的挡板跨过左上角和右下角，在网格中用 1 表示。
- 将球导向左侧的挡板跨过右上角和左下角，在网格中用 -1 表示。

在箱子每一列的顶端各放一颗球。每颗球都可能卡在箱子里或从底部掉出来。如果球恰好卡在两块挡板之间的 "V" 形图案，或者被一块挡导向到箱子的任意一侧边上，就会卡住。

返回一个大小为 n 的数组 answer ，其中 answer[i] 是球放在顶部的第 i 列后从底部掉出来的那一列对应的下标，如果球卡在盒子里，则返回 -1 。

> 来源：力扣（LeetCode）
> 链接：https://leetcode-cn.com/problems/where-will-the-ball-fall
> 著作权归领扣网络所有。

示例：
> 输入：grid = [[1,1,1,1,1,1],[-1,-1,-1,-1,-1,-1],[1,1,1,1,1,1],[-1,-1,-1,-1,-1,-1]]
> 输出：[0,1,2,3,4,-1]

<!--more-->

## 题解
使用DFS模拟球落下的过程：
首先，需要将每个小球分别进行模拟。所以，需要使用循环从 `0` 到 `grid[0].length` 的球；循环内将进行`dfs`查找，将查找的结果存入数组 `re` ；

![image.png](https://img-blog.csdnimg.cn/img_convert/f02d309bca64233b87ccbedadf30a6fb.png)

dfs函数：
- 接收两个参数，第一个是开始的行数（从第一行开始 - 即为0），第二个是开始的列数。
- 返回查到的小球出去的列数。
    - 当行数 `row` 大于等于`grid`的行数时，就证明小球掉出来了，返回此时的列数；
- 小球一共有三种情况：
    - 小球位于第一列
        - 此时，小球能否出来就看这个格子和右边的那个格子；
        - `|\\`，此时小球可以进入右下角的那个格子。即递归调用： `return dfs(row+1,col+1)` 
        - `|\/`、`|/\` 或 `|//`，此时小球都会卡住，此时 `return -1` 即可
    - 小球位于最后一列
        - 此时，小球能否出来就看这个格子和左边的那个格子；
        - `//|`，此时小球可以进入左下角的那个格子。即递归调用： `return dfs(row+1,col-1)` 
        - `\/|`、`\\|` 或 `/\|`，此时小球都会卡住，此时 `return -1` 即可
    - 小球处于一般位置
    - 分两种情况：
        - 本格子为 -1 ,即`/`:
        - 此时，小球能否出来就看这个格子和左边的那个格子；
            - `//`，此时小球可以进入左下角的那个格子。即递归调用： `return dfs(row+1,col-1)` 
            - `\/`此时小球都会卡住，此时 `return -1` 即可
        - 本格子为 1，即 `\`：
            - 此时，小球能否出来就看这个格子和右边的那个格子；
            - `\\`，此时小球可以进入右下角的那个格子。即递归调用： `return dfs(row+1,col+1)` 
            - `\/`，此时小球都会卡住，此时 `return -1` 即可

### 代码

```javascript
var findBall = function(grid) {
    if(grid[0].length === 1) return [-1];
    let re = [];
    const dfs = (row,col) => {
        if(row >= grid.length) return col;
        if(col === 0){
            if(grid[row][col] === -1 || grid[row][col+1] === -1){
                return -1;
            } else  return dfs(row+1,col+1);
        } else if(col === grid[0].length-1){
            if(grid[row][col] === 1 || grid[row][col-1] === 1){ // "\|"
                return -1;
            } else return dfs(row+1,col-1);
        } else if(grid[row][col] === 1){
            if(grid[row][col+1] === 1){ // "\\"
                return dfs(row+1,col+1);
            } else return -1;
        } else if(grid[row][col] === -1){
            if(grid[row][col-1] === -1){ // "//"
                return dfs(row+1,col-1);
            } else  return -1;
        }
    }
    for(let i = 0;i<grid[0].length;++i){
        re.push(dfs(0,i));
    }
    return re;
};
```

