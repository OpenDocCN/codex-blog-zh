# Leetcode[36]:有效的数独

> 原文：<https://medium.com/codex/leetcode-valid-sudoku-35e94d524823?source=collection_archive---------11----------------------->

## 今日报价

> 我是那个该死的时候就会死的人，所以让我以自己喜欢的方式生活吧。

![](img/7ad298ef1ca9fa7e6be4fbf71be8759b.png)

照片由[西格蒙德](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

确定`9 x 9`数独板是否有效。根据以下规则，仅需要验证填充的单元格**:**

1.  每行必须包含数字`1-9`，不能重复。
2.  每一列必须包含数字`1-9`，不能重复。
3.  网格的九个`3 x 3`子框必须包含数字`1-9`，不能重复。

**注:**

*   数独棋盘(部分填充)可能是有效的，但不一定是可解的。
*   只有填充的单元格需要根据上述规则进行验证。

## 解决办法

我们可以把它分成三个任务

1.  验证**行**(每行必须只包含一次从 0 到 9 的值)
2.  验证**列**(每个列只能包含一次从 0 到 9 的值)
3.  验证 3x3 **框**(每个 3x3 框只能包含一次 0-9 的值)

因为我们知道棋盘中的每一个元素要么是(。)表示一个空格或“0–9”中的任何数字字符。

所以如果我们遇到“.”我们不在乎，如果我们遇到数字字符，我们会检查我们之前是否遇到过。

我们可以使用 HashSet 来检查被访问的数字字符，但是我们可以使用大小为 10 的布尔数组，因为我们知道每个数字字符都是“0–9”。

现在我们必须从数字字符中提取索引，这里我们使用一些技巧来得到它。

我们知道字符数字在 ASCII 格式中是连续的。

因此，如果我们尝试减去 ASCII 值，我们将得到相应的数字。

现在让我们看看代码-

```
class Solution {
    public boolean isValidSudoku(char[][] board) {

        int rowSize = 9;
        int colSize = 9;

        // validate rows
        for(int i=0;i<rowSize;i++) {

            if(!isValidRow(board, i, colSize))
                return false;
        }

        // validate cols
        for(int i=0;i<colSize;i++) {

            if(!isValidCol(board, i, rowSize))
                return false;
        }

        // validate boxes
        for(int i=0;i<9;i+=3) {
            for(int j=0;j<9;j+=3) {
                // i and j are the starting index of the box
                if(!isValidBox(board, i, j)) 
                    return false;
            }
        }

        return true;
    }

    public boolean isValidRow(char[][] board, int row, int colSize) {

        boolean[] visited = new boolean[colSize+1];

        for(int i=0;i<colSize;i++) {

            if(board[row][i] == '.') continue;

            int ind = board[row][i]-'0';

            if(visited[ind]) return false;

            visited[ind] = true;
        }

        return true;
    }

    public boolean isValidCol(char[][] board, int col, int rowSize) {

        boolean[] visited = new boolean[rowSize+1];

        for(int i=0;i<rowSize;i++) {

            if(board[i][col] == '.') continue;

            int ind = board[i][col]-'0';

            if(visited[ind]) return false;

            visited[ind] = true;
        }

        return true;
    }

    public boolean isValidBox(char[][] board, int row, int col) {

        boolean[] visited = new boolean[10]; // 0 - 9

        for(int i=0;i<3;i++) {
            for(int j=0;j<3;j++) {

                if(board[row+i][col+j] == '.') continue;

                int ind = board[row+i][col+j]-'0';

                if(visited[ind]) return false;

                visited[ind] = true;
            }
        }

        return true;
    }
}
```

## 摘要

在整个代码中，只有一件事对我来说很重要，那就是使用布尔数组来检查以前访问过的字符。(仅我的观点)。

## 谢谢大家(来自一个非常内向的男孩)