## 79.单词搜索

**给定一个二维网格和一个单词，找出该单词是否存在于网格中。**

**单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。**

**示例:**

```
board =
[  
    ['A','B','C','E'],  
    ['S','F','C','S'],  
    ['A','D','E','E']
]

给定 word = "ABCCED", 返回 true
给定 word = "SEE", 返回 true
给定 word = "ABCB", 返回 false
```

> 解题思路(有点暴力搜索的感觉)：dfs(深度优先搜索)和回溯算法，找到首字母进入递归开始四个方向搜索与回溯算法搭配使用，记录走过的位置，回溯释放走过的位置，直到检索到所有字母或将所有表格字母检索完！



### Python

```python
class Solution:
    direction = [(0, -1), (-1, 0), (0, 1), (1, 0)]

    def exist(self, board, word):
        m = len(board)
        if(m == 0):
            return False
        n = len(board[0])
        marked = [[0 for _ in range(n)] for _ in range(m)]

        for i in range(m):
            for j in range(n):
                # 对每一个格子都需要从头搜索
                if self.__search_word(board, word, 0, i, j, m, n, marked):
                    return True

        return False


    def __search_word(self, board, word, index, i, j, m, n, marked):
        # 递归终止条件
        if index == len(word) - 1:
            return board[i][j] == word[index]

        if board[i][j] == word[index]:
            # 标记已经走过的格子,如果搜索不成功需要释放掉
            marked[i][j] = 1
            for direction in self.direction:
                new_x = i + direction[0]
                new_y = j + direction[1]
                if 0 <= new_x < m and 0 <= new_y < n and not marked[new_x][new_y] and self.__search_word(board, word, index + 1, new_x, new_y, m, n, marked):
                    return True
            # 回溯,表示搜索哦不成功，释放位置
            marked[i][j] = 0
        return False
```



### C++

```c++
class Solution {
public:
	// 四个遍历方像的值
	int director[4][2] = { {0, -1}, {-1, 0}, {0, 1}, {1, 0} };
	bool exist(vector<vector<char>>& board, string word) {
		int m = board.size();
		if (m == 0) return false;
		int n = board[0].size();

		vector<vector<int>> marked(m, vector<int>(n));
		for (int i = 0; i < m; ++i)
		{
			for (int j = 0; j < n; ++j)
			{
				if (dfs(board, word, 0, i, j, m, n, marked)) return true;
			}
		}

		return false;
	}

	bool dfs(vector<vector<char>>& board, string word, int index, int i, int j, int m, int n, vector<vector<int>>& marked)
	{
		// 递归结束条件
		if (index == word.size() - 1) return board[i][j] == word[index];

		if (board[i][j] == word[index])
		{
			// 记录走过的位置
			marked[i][j] = 1;
			for (int k = 0; k < 4; ++k)
			{
				int new_x = i + director[k][0];
				int new_y = j + director[k][1];

				if ((new_x >= 0 && new_x < m) && (new_y >= 0 && new_y < n) && (!marked[new_x][new_y]) &&
					dfs(board, word, index + 1, new_x, new_y, m, n, marked))
					return true;
			}
			// 回溯 释放走过的位置
			marked[i][j] = 0;
		}

		return false;
	}
};
```

