#### 200. 岛屿数量

**给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。**

**岛屿总是被水包围，并且每座岛屿只能由水平方向或竖直方向上相邻的陆地连接形成。**

**此外，你可以假设该网格的四条边均被水包围。**

**示例:**

```
输入:
11110
11010
11000
00000
输出: 1

输入:
11000
11000
00100
00011
输出: 3
解释: 每座岛屿只能由水平和/或竖直方向上相邻的陆地连接而成。
```

> 解题思路：连通区域的求解，找到数组中的'1'，进行连通区域的遍历，遍历过的修改成'0'，最后经过多少次DFS搜索即有多少岛屿！



### C++ --- DFS(深度优先递归方式)

```c++
class Solution
{
public:
	int direction[4][2] = { {0,-1},{-1,0},{0,1},{1,0} };
	int num_island(vector<vector<char>>& grid)
	{
		int m = grid.size();
		if (m == 0) return 0;
		int n = grid[0].size();

		int num_index = 0;
		for (int i = 0; i < m; ++i)
		{
			for (int j = 0; j < n; ++j)
			{
				if (grid[i][j] == '1')
				{
					num_index++;
					dfs(grid, i, j);
				}
			}
		}

		return num_index;
	}

	void dfs(vector<vector<char>>& grid, int i, int j)
	{
		grid[i][j] = '0';
		for (int k = 0; k < 4; ++k)
		{
			int new_x = i + direction[k][0];
			int new_y = j + direction[k][1];

			if (new_x >= 0 && new_x < grid.size() && new_y >= 0 && new_y < grid[0].size() && grid[new_x][new_y] == '1')
			{
				dfs(grid, new_x, new_y);
			}
		}
	}
};
```



### Python --- BFS(广度优先搜索)

```python
from collections import deque
class Solution:
    direction = [(0, -1), (-1, 0), (0, 1), (1, 0)]
    def num_island(self, grid):
        m = len(grid)
        if m == 0:
            return 0
        n = len(grid[0])
        queue = deque()

        num_index = 0
        for i in range(m):
            for j in range(n):
                if grid[i][j] == '1':
                    num_index += 1
                    grid[i][j] = '0'
                    queue.append((i, j))
                    while queue:
                        row, col = queue.popleft()
                        for direction in self.direction:
                            new_x = row + direction[0]
                            new_y = col + direction[1]
                            if 0 <= new_x < m and 0 <= new_y <n and grid[new_x][new_y] == '1':
                                queue.append((new_x, new_y))
                                grid[new_x][new_y] = '0'

        return num_index
```



### Python 并查集实现

> 通过邻接点建立点值之间的根植索引     运行的时间和空间复杂度相比以上两个算法都比较高

```python
class Solution:
    def num_island(self, grid):
        """
        Do not return anything, modify board in-place instead.
        """
        print(grid)
        f = {}

        def find(x):
            f.setdefault(x, x)  # 表示字典里面有的不更新，没有的元素更新键和值相等的键值对
            if f[x] != x:
                f[x] = find(f[x])
            return f[x]

        def union(x, y):
            f[find(y)] = find(x)

        row = len(grid)
        if row == 0:
            return 0
        col = len(grid[0])

        for i in range(row):
            for j in range(col):
                if grid[i][j] == "1":
                    find(i * col + j)   # 只有单个的连通区域
                    for x, y in [(-1, 0), (1, 0), (0, -1), (0, 1)]:
                        new_x = i + x
                        new_y = j + y
                        if 0 <= new_x < row and 0 <= new_y < col and grid[new_x][new_y] == "1":
                            union(i * col + j, new_x * col + new_y)

        num_index = []
        for i in range(row):
            for j in range(col):
                if grid[i][j] == '1':
                    temp = find(i * col + j)
                    if not num_index:
                        num_index.append(temp)
                    else:
                        if temp not in num_index:
                            num_index.append(temp)

        return len(num_index)
```

