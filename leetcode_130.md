## 130.被围绕的区域

**给定一个二维的矩阵，包含 `'X'` 和 `'O'`（字母 O）。**

**找到所有被 `'X'` 围绕的区域，并将这些区域里所有的 `'O'` 用 `'X'` 填充。**

**示例:**

```
X X X X
X O O X
X X O X
X O X X

运行你的函数后，矩阵变为：
X X X X
X X X X
X X X X
X O X X

解释:
被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。
任何不在边界上，或不与边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。
```

> 解题思路：将问题转化为寻找边界上的'O'的连通区域，找到后修改为'#'，最后将二维数组中的'O'改为'X'，将'#'换回'O'。采用 DFS(递归) + DFS(非递归) + BFS + 并查集 方法求解！



### Python --- DFS(递归)

```python
class Solution:
    def solve(self, board):
        m = len(board)
        if m == 0:
            return None
        n = len(board[0])

        for i in range(m):
            for j in range(n):
                isEdge = i == 0 or j == 0 or i == m-1 or j == n-1
                if isEdge and board[i][j] == 'O':
                    self.__dfs(board, i, j, m, n)

        for i in range(m):
            for j in range(n):
                if board[i][j] == 'O':
                    board[i][j] = 'X'
                if board[i][j] == '#':
                    board[i][j] = 'O'

        return None


    def __dfs(self, board, i, j, m, n):
        if i < 0 or i >= m or j < 0 or j >= n or board[i][j] == '#' or board[i][j] == 'X':
            return None

        board[i][j] = '#'
        self.__dfs(board, i, j-1, m, n)
        self.__dfs(board, i-1, j, m, n)
        self.__dfs(board, i, j+1, m, n)
        self.__dfs(board, i+1, j, m, n)
        return None
```



### C++ --- DFS(非递归)

```c++
// 使用栈数据结构深度优先搜索
class Solution {
	typedef struct Pos
	{
		int i, j;
	}pos;
public:
	void solve(vector<vector<char>>& board)
	{
		int m = board.size();
		if (m == 0) return;
		int n = board[0].size();

		for (int i = 0; i < m; ++i)
		{
			for (int j = 0; j < n; ++j)
			{
				bool isEdge = (i == 0 || j == 0 || i == m - 1 || j == n - 1);
				if (isEdge && board[i][j] == 'O')
					dfs(board, i, j, m, n);
			}
		}

		for (int i = 0; i < m; ++i)
		{
			for (int j = 0; j < n; ++j)
			{
				if (board[i][j] == 'O')
					board[i][j] = 'X';
				if (board[i][j] == '#')
					board[i][j] = 'O';
			}
		}

	}

	void dfs(vector<vector<char>>& board, int i, int j, int m, int n)
	{
		stack<pos> s;
		
		pos p;
		p.i = i;
		p.j = j;
		s.push(p);
		board[i][j] = '#';
		while (!s.empty())
		{
			pos t = s.top();
			
			// 上
			if (t.j - 1 >= 0 && board[t.i][t.j - 1] == 'O')
			{
				board[t.i][t.j - 1] = '#';
				p.i = t.i;
				p.j = t.j - 1;
				s.push(p);
				continue;
			}

			// 左
			if (t.i - 1 >= 0 && board[t.i - 1][t.j] == 'O')
			{
				board[t.i - 1][t.j] = '#';
				p.i = t.i - 1;
				p.j = t.j;
				s.push(p);
				continue;
			}

			// 下
			if (t.j + 1 < n && board[t.i][t.j + 1] == 'O')
			{
				board[t.i][t.j + 1] = '#';
				p.i = t.i;
				p.j = t.j + 1;
				s.push(p);
				continue;
			}

			// 右
			if (t.i + 1 < m && board[t.i + 1][t.j] == 'O')
			{
				board[t.i + 1][t.j] = '#';
				p.i = t.i + 1;
				p.j = t.j;
				s.push(p);
				continue;
			}
			s.pop();
		}
	}
};
```



### C++ --- BFS(非递归)

```c++
// 使用队列数据结构进行广度优先搜索
class Solution {
	typedef struct Pos
	{
		int i, j;
	}pos;
public:
	void solve(vector<vector<char>>& board)
	{
		int m = board.size();
		if (m == 0) return;
		int n = board[0].size();

		for (int i = 0; i < m; ++i)
		{
			for (int j = 0; j < n; ++j)
			{
				bool isEdge = (i == 0 || j == 0 || i == m - 1 || j == n - 1);
				if (isEdge && board[i][j] == 'O')
					bfs(board, i, j, m, n);
			}
		}

		for (int i = 0; i < m; ++i)
		{
			for (int j = 0; j < n; ++j)
			{
				if (board[i][j] == 'O')
					board[i][j] = 'X';
				if (board[i][j] == '#')
					board[i][j] = 'O';
			}
		}

	}

	void bfs(vector<vector<char>>& board, int i, int j, int m, int n)
	{	
		queue<pos> s;
		pos p;
		p.i = i;
		p.j = j;
		s.push(p);
		board[i][j] = '#';
		while (!s.empty())
		{
			pos t = s.front();		
			// 上
			if (t.j - 1 >= 0 && board[t.i][t.j - 1] == 'O')
			{
				board[t.i][t.j - 1] = '#';
				p.i = t.i;
				p.j = t.j - 1;
				s.push(p);
			}

			// 左
			if (t.i - 1 >= 0 && board[t.i - 1][t.j] == 'O')
			{
				board[t.i - 1][t.j] = '#';
				p.i = t.i - 1;
				p.j = t.j;
				s.push(p);
			}

			// 下
			if (t.j + 1 < n && board[t.i][t.j + 1] == 'O')
			{
				board[t.i][t.j + 1] = '#';
				p.i = t.i;
				p.j = t.j + 1;
				s.push(p);
			}

			// 右
			if (t.i + 1 < m && board[t.i + 1][t.j] == 'O')
			{
				board[t.i + 1][t.j] = '#';
				p.i = t.i + 1;
				p.j = t.j;
				s.push(p);
			}

			s.pop();
		}
	}
};
```

### 并查集

> 通过邻接点建立点值之间的根植索引

```c++
// C++
// 通过find函数建立邻接点的根值索引
class Solution {
public:
	int director[4][2] = { {0,-1},{-1,0},{0,1},{1,0} };
	unordered_map<int, int> m;
	int find(int x)
	{
		auto it = m.find(x);
		if (it == m.end())
		{
			m[x] = x;
		}
		
		if (m[x] != x)
		{
			m[x] = find(m[x]);
		}

		return m[x];
	}

	void con_union(int x, int y)
	{
		m[find(y)] = find(x);
	}

	void solve(vector<vector<char>>& board)
	{
		int row = board.size();
		if (row == 0) return;
		int col = board[0].size();
		int total_num = row * col;

		for (int i = 0; i < row; ++i)
		{
			for (int j = 0; j < col; ++j)
			{
				if (board[i][j] == 'O')
				{
					if (i == 0 || j == 0 || i == row - 1 || j == col - 1)
					{
						con_union(i*col + j, total_num);
					}
					else
					{
						for (int k = 0; k < 4; ++k)
						{
							if (board[i + director[k][0]][j + director[k][1]] == 'O')
							{
								con_union(i*col + j, (i + director[k][0])*col + j + director[k][1]);
							}	
						}
					}
				}
			}
		}

		for (int i = 0; i < row; ++i)
		{
			for (int j = 0; j < col; ++j)
			{
				if (find(total_num) == find(i*col + j))
				{
					board[i][j] = 'O';
				}
				else
				{
					board[i][j] = 'X';
				}
			}
		}
	}
};
```

```python
// Python
class Solution:
    def solve(self, board):
        """
        Do not return anything, modify board in-place instead.
        """
        f = {}

        def find(x):
            f.setdefault(x, x)
            if f[x] != x:
                f[x] = find(f[x])
            return f[x]

        def union(x, y):
            f[find(y)] = find(x)

        if not board or not board[0]:
            return
        row = len(board)
        col = len(board[0])
        dummy = row * col
        for i in range(row):
            for j in range(col):
                if board[i][j] == "O":
                    if i == 0 or i == row - 1 or j == 0 or j == col - 1:
                        union(i * col + j, dummy)
                    else:
                        for x, y in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
                            if board[i + x][j + y] == "O":
                                union(i * col + j, (i + x) * col + (j + y))

        for i in range(row):
            for j in range(col):
                if find(dummy) == find(i * col + j):
                    board[i][j] = "O"
                else:
                    board[i][j] = "X"
```

