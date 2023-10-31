# 搜索探寻与问题求解

## 搜索基本概念

### 搜索的形式化描述

!!! note "状态（state）"
    广义来说状态是对搜索算法和搜索环境当前所处情形的描述信息。搜索算法刚开始所在状态被称为初始状态，完成任务时所在状态被称为终止状态。

!!! note "动作（ action）"
    广义来说状态是对搜索算法和搜索环境当前所处情形的描述信息。为了完成最短路径的探索，算法需要不断从一个状态转移到下一个状态。算法从一个状态转移到另外一个状态所采取的行为被称为动作。

!!! note "状态转移（state transition）"
    算法选择了一个动作之后，其所处状态也会发生相应变化，这个过程被称为状态转移。

!!! note "路径（path）和代价（cost）"
    以任何一个状态为起点，搜索算法通过执行一系列动作后，将会在不同状态之间不断转移。将这个过程中经历的状态记录下来，可以得到一个状态序列，这个状态序列被称为一条路径。每条路径对应一个代价。如果一条路径对应的状态序列长度为2，那么这条路径的代价也被称为单步代价。一般假定单步代价取值为非负。

!!! note "目标测试（goal test）"
    目标测试函数goal_test(𝑠)用于判断状态𝑠是否为目标状态。目标测试通过意味着搜索算法完成，但是搜索算法完成并不意味着算法已经找到了最短路径。在最短路径搜索中，搜索算法会不断从当前结点的邻接节点中选择下一个结点，如此循环，直到找到一条从初始状态到终止状态的路径为止。当然，寻找所得的路径（从初始状态到终止状态）是否满足“路径最短”这一约束条件，还需要另行判断。

!!! note "搜索过程可视为搜索树的构建"

    可以用一棵树来记录算法所探索的路径，这样一棵树被称为搜索树，树中每个结点用一个状态标记。搜索树记录了从根结点出发目前所有探索过的路径。搜索算法可以被看成是一个构建搜索树的过程，从根结点（初始状态）开始，不断展开每个结点的后继结点，直到某个结点通过了目标测试。

### 搜索算法的评测标准

- 评价指标
    - 完备性：能否找到解（不一定最优）
    - 最优性：能否保证找到的第一个解是最优解
    - 时间复杂度（通过扩展的结点数量衡量）
    - 空间复杂度（通过同时记录的结点数量衡量）

!!! note ""
    ![Alt text](images/image-20.png)

### 搜索框架

```text
F <- {根节点}
while F != ∅ do
    n <- pick_from(F)
    F <- F - {n}
    if goal_test(n) then
        return n.path
    end
    F <- F ∪ successor_nodes(n)
end
```

- pick_from 决定扩展结点的顺序，successor_nodes 决定哪些节点可被放入边缘集合（fringe set，也叫开表，open list）以在后面扩展（expand）
- 每次从边缘集合中取出最上层（最浅）的结点时是广度优先搜索（breadth first search，BFS）
- 每次从边缘集合中取出最下层（最深）的结点时是深度优先搜索（depth first search，DFS）
- 放弃扩展部分结点的做法称为剪枝（pruning）

## 贪婪最佳优先搜索

- 利用一些能够辅助算法做出决策的额外信息的搜索算法称为启发式搜索（heuristic search），或有信息搜索（informed search）
- 提供的这些辅助信息称为启发信息
- 启发信息通常形式化为一个关于结点的函数 $h(n)$，其用于估计结点 $n$ 距离达到目标还需付出多少代价，这个函数称为启发函数（heuristic function）
    - 启发函数通常是非负的
    - 常见用法是用来更改前面的 pick_from 函数来规定挑选结点的顺序
- 对于任意结点 $n$，决定了搜索算法扩展结点 $n$ 的优先度的函数 $f(n)$ 称为评价函数（evaluation function）
    - 评价函数值越小，被挑选的优先级越高
    - 深度优先搜索中 $f(n)$ 可被定义为该结点深度的倒数
    - 广度优先搜索中 $f(n)$ 可被定义为该结点深度
- 贪婪最佳优先搜索
    - 即 greedy best-first search，GBFS
    - 优先扩展距离目标近的结点，即令 $f(n) = h(n)$
    - 不排除环路的贪婪最佳优先搜索算法是不完备的
    - 排除环路的贪婪最佳优先搜索是完备的，但不一定最优
    - 最坏情况下的时间复杂度和空间复杂度均为 $O(b^m)$
        - $b$ 为分支因子（每个结点最大的分支数目）
        - $m$ 为最大深度，也就是搜索树中路径的最大可能长度

- 智能体不唯一，解决信息确定、全局可观察、轮流行动、输赢收益零和的博弈问题，求解这样问题的算法称为对抗搜索（adversarial search）或博弈搜索（game search）
- 智能体会选择最大化自身利益、最小化对手利益的策略
- 形式化描述：
    - 状态：状态 $s$ 包括当前游戏局面和当前行动的智能体，初始状态 $s_0$ 为游戏开始时的状态。$\mathrm{player}(s)$ 表示状态 $s$ 下行动的智能体
    - 动作：动作是指 $\mathrm{player}(s)$ 在当前局面下可以采取的操作 $a$，记动作集合为 $\mathrm{actions}(s)$
    - 状态转移：状态转移函数 $s' = \mathrm{result}(s, a)$ 表示在状态 $s$ 下采取动作 $a$ 后的下一个状态
    - 终局状态测试：终局状态测试函数 $\mathrm{terminal\_test}(s)$ 用于测试游戏是否在状态 $s$ 下结束
    - 终局得分：终局得分函数 $\mathrm{utility}(s, p)$ 表示在状态 $s$ 下玩家 $p$ 的得分
        - 对于二人零和博弈，只需要记录其中一人的终局得分即可

### 最大最小搜索

- 最大最小搜索（minimax search）是求解对抗搜索问题的基本算法
- 该算法假设两名玩家在决策时总是理性地倾向于最大化自己的得分（最小化对方得分）
- 算法过程
    - 假设以最大化得分为目标的玩家为 MAX，以最小化得分为目标的玩家为 MIN
    - 某一层由 MAX 玩家行动，则其会选择得分最大的子树进行行动
    - 某一层由 MIN 玩家行动，则其会选择得分最小的子树进行行动
    - 递归地进行上述过程，直到达到终局状态
    - （子树的得分由所有它的子树的得分取最大或最小得到）

$$
\mathrm{minimax}(s) = \begin{cases}
\mathrm{utility}(s) & \text{if terminal\_test(}s\text{)} \\
\max_{a \in \mathrm{actions}(s)} \mathrm{minimax}(\mathrm{result}(s, a)) & \text{if player(}s\text{) = MAX} \\
\min_{a \in \mathrm{actions}(s)} \mathrm{minimax}(\mathrm{result}(s, a)) & \text{if player(}s\text{) = MIN}
\end{cases}
$$

- 最大最小搜索的时间复杂度为 $O(b^m)$，空间复杂度为 $O(bm)$

### Alpha-Beta 剪枝

- 如果搜索树极大，则最大最小搜索的开销巨大，无法在合理时间内返回结果
- Alpha-Beta 剪枝算法的思想如下：

    $$
    \begin{align*}
    \mathrm{minimax}(s_0) &= \max(\min(3, 9, 10), \min(2, x, y), \min(10, 5, 1))\\
    &= \max(3, \min(2, x, y), 1)
    \end{align*}
    $$

    - 上式中 $\min(2, x, y)$ 肯定小于 2，而外面一层求最大值又有 3 比它大
    - 所以就没有必要去搜索 $x, y$ 对应的子树得到具体的 $x, y$ 值，可以将这两个动作剪枝掉

## 蒙特卡洛树搜索

- 四个步骤
    - 选择（selection）：算法从搜索树的根节点开始，向下递归选择子节点直到到达叶子结点或者到达还具有未被扩展的子节点的节点 L，向下递归选择的过程可由 UCB1 算法来实现，在递归选择过程中记录下每个节点被选择的次数和每个节点得到的奖励均值
    - 扩展（expansion）：如果节点 L 还不是一个终止节点，则随机扩展它的一个未被扩展过的后继边缘节点 M
    - 模拟（simulation）：从节点 M 出发，模拟扩展搜索树，直到找到一个终止节点
    - 反向传播（back propagation）：用模拟所得结果回溯更新模拟路径中 M 及以上节点的奖励均值和被访问次数

??? note "代码"
    ```python
    class AIPlayer:
        def **init**(self, color, C=1.2, iterations=100):
            self.player = color
            self.C = C
            self.iterations = iterations

        def game_over(self, board):
            # 根据当前棋盘，判断棋局是否终止
            # 如果当前选手没有合法下棋的位子，则切换选手；如果另外一个选手也没有合法的下棋位置，则比赛停止。
            b_list = list(board.get_legal_actions('X'))
            w_list = list(board.get_legal_actions('O'))
            is_over = len(b_list) == 0 and len(w_list) == 0  # 返回值 True/False
            return is_over

        def uct_search(self, root, iterations):
            for i in range(iterations):
                node = self.select(root)
                reward = self.simulate(node)
                self.backpropagate(node, reward)
            return self.ucb(root, 0).action

        def select(self, node):
            while not self.game_over(node.board):
                if len(node.children) == 0 or not node.finished():
                    return self.expand(node)
                else:
                    node = self.ucb(node, self.C)
            return node
        
        def expand(self, node):
            actions = list(node.board.get_legal_actions(node.player))
            if len(actions) == 0:
                return node.parent
            visited = [child.action for child in node.children]
            actions = [action for action in actions if action not in visited]
            action = random.choice(actions)
            new_board = deepcopy(node.board)
            new_board._move(action, node.player)
            node.expend(new_board, action, 'X' if node.player == 'O' else 'O')
            return node.children[-1]
        
        def ucb(self, node, C):
            max_ucb = -float('inf')
            max_children = []
            for child in node.children:
                ucb = child.reward / child.cnt + C * math.sqrt(2 * math.log(node.cnt) / child.cnt)
                if ucb > max_ucb:
                    max_ucb = ucb
                    max_children = [child]
                elif ucb == max_ucb:
                    max_children.append(child)
            if len(max_children) == 0:
                return node.parent
            return random.choice(max_children)
        
        def simulate(self, node):
            board = deepcopy(node.board)
            player = node.player
            while not self.game_over(board):
                actions = list(board.get_legal_actions(player))
                if not len(actions) == 0:
                    action = random.choice(actions)
                    board._move(action, player)
                player = 'X' if player == 'O' else 'O'
            return self.evaluate(board)
        
        def evaluate(self, board: Board):
            winner, diff = board.get_winner()
            if winner == 2:
                return 0
            winner = 'X' if winner == 0 else 'O'
            if winner == self.player:
                return 10 + diff
            else:
                return -10 - diff

        def backpropagate(self, node: TreeNode, reward):
            while node:
                node.cnt += 1
                if node.player == self.player:
                    node.reward -= reward
                else:
                    node.reward += reward
                node = node.parent

        def get_move(self, board):
            if self.player == 'X':
                player_name = '黑棋'
            else:
                player_name = '白棋'
            print("请等一会，对方 {}-{} 正在思考中...".format(player_name, self.player))
            root = TreeNode(deepcopy(board), None, None, self.player)
            action = self.uct_search(root, self.iterations)
            return action
    ```
