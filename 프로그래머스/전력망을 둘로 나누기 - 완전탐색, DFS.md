# 전력망을 둘로 나누기 : 완전탐색, DFS or BFS, 그래프, 트리 ⭐️ ( 좋은 문제 ) → 문서 들어오면 딴 거 풀지 말고 이거 정답으로 공부하자.

- 생각
  - 정답 자체는 웹에서 검색해서 찾고 그것의 해설을 ai에게 맡기자
  - ai에게 주니까 너무 어려운 코드로 짠다.
  - ai가 최초에 뱉은 걸로만 매번 공부하지 말고, 코드를 더 쉽게 못 만드냐고 묻는다.
  - 이거 2단계라서 만만하니까, 조급하게 다른 거 넘어가기 전에 이거부터 흡수 하자. 좋아 보인다.
  - 잘 모르겠을 때는 하나하나에 매몰되지 말고, 그냥 판단 보류하고 전체를 쭉 적어라. 일단 기본 재료를 흡수, 암기가 되어야 조립하며 이해할 수 있다.
  - 일단 외우고, 나중에 “아~ 이게 이런거였구나” 이런 순으로 가야한다. 아니면 답 없다. 어휘부터 외워야 문장이 이해가 되는 것이다.
- 공부
  - for (const auto& wire : wires) { int u = wire[0]; int v = wire[1]; // 이렇게 여러번 쓸 값은, for 문 안에 있는 계속 바뀌는 값이여도 변수로 할당을 해준다.
  - 그래프를 구성하는 방식을 흡수해야한다.
  - 그리고 그 안을 DFS로 훑는 것도
  - 좋은 문제 같다. DFS, 트리, 그래프 기본기, 경험이 부족한 나에게 말이다.
- 재귀
  - 재귀의 형태
    - base case
      - 언제 멈출 건지 초기에 명시
    - 할 것 하나 하고 다음으로 넘기기
      - 자기 자신을 호출해서 파고들어간다.
  - 원리
    - 마트료시카같은거다. 계속 열고, 다음 거 나오고, 열고 하다가…
    - 끝에 도달해서 뭐가 하나 걸려서 return으로 가게 되면, 이제 그 값을 갖고 끝에서부터 다시 하나하나 닫으면서 값을 반환하는 것이다.
  - 흐름
    - a ( 대기 ) → b
    - b ( 대기 ) → c
    - c ( 대기 ) → d ( 조건 빠져나가서 return )
    - d → c ( 값 받아서 마저 진행, 반환 )
    - c → b ( 값 받아서 마저 진행, 반환 )
    - b → a ( 값 받아서 마저 진행, 반환 )
- 용어
  - 인접 리스트란?
    - 그래프의 각 정점에 대해서 인접한 ( 간선으로 연결된 ) 정점들을 연결 리스트로 표현한 것이다.
    - 각 정점에 대해 연결된 정점의 정보가 각각의 정점에 해당하는 리스트 항목에 저장되는 것이다.
  - 무방향 트리
    - 루트가 없다.
    - 특징으로는, parent라는 용어를 쓰긴 하지만, 그래프 자체에 위계가 있다기 보다, dfs로 탐색을 하면서 탐색 순서에 따라 임시적으로 위계가 생기기 때문에 쓴다.
    - 그래프를 구성할 때 양방향으로 정보를 넣어서, 무한으로 돌지 않게 하려고 방향을 고정해주는 역할을 한다.
  - 벡터를 이용한 그래프에서의 인접 리스트 구성
    - 구현 방식
      - 각 송전탑 ( 번호 ) 마다 연결된 송전탑을 적어두는 공간을 둔다. ( 2중 벡터 이용 )
      - 이걸로 인접 리스트는 구현된다.
      - 그리고 dfs로 그 안을 순회한다.
- DFS 개선 코드 ( visited )
  ```cpp
  #include <string>
  #include <vector>
  #include <cmath>
  #include <algorithm>

  using namespace std;

  // DFS를 이용하여 연결된 송전탑의 개수를 세는 함수
  int dfs(int current, int parent, const vector<vector<int>>& graph) {
      int count = 1; // 자기 자신을 포함하므로 1부터 시작
      // 현재 노드와 연결된 다른 노드들을 탐색
      for (int next : graph[current]) {
          // 방금 넘어온 부모 노드이거나, 가짜 부모로 설정한 차단 노드라면 무시
          if (next != parent) {
              count += dfs(next, current, graph);
          }
      }
      return count;
  }

  int solution(int n, vector<vector<int>> wires) {
      int answer = n;

      // 1. 트리(그래프) 구성
      vector<vector<int>> graph(n + 1);
      for (const auto& wire : wires) {
          graph[wire[0]].push_back(wire[1]);
          graph[wire[1]].push_back(wire[0]);
      }

      // 2. 모든 전선을 하나씩 끊어보며 확인
      for (const auto& wire : wires) {
          int u = wire[0];
          int v = wire[1];

          // 3. 한쪽 그룹의 송전탑 개수 세기
          // 핵심 로직: v를 부모 노드로 취급하여 u부터 탐색 시작 (전선을 끊은 것과 동일한 효과)
          int count_group1 = dfs(u, v, graph);
          // 어차피 parent == next이면 더 진행 안하니까 한 쪽을 부모로 취급하면 한 방향으로 쭉 가고 끝에서 멈춘다.

          // 4. 반대쪽 그룹의 개수와 두 그룹의 차이 계산
          int count_group2 = n - count_group1;
          int diff = abs(count_group1 - count_group2);

          // 최솟값 갱신
          answer = min(answer, diff);
      }
      return answer;
  }
  ```
- DFS 정답 코드
  - [이면지](https://app.notion.com/p/3e9526eb9c27804889d4fc038b8bf831?pvs=21)
  ```cpp
  #include <string>
  #include <vector>
  #include <cmath>
  #include <algorithm>

  using namespace std;

  // DFS를 이용하여 연결된 송전탑의 개수를 세는 함수
  int dfs(int current, int parent, const vector<vector<int>>& graph) {
      // 원본에 영향을 주도록 &를 붙어야 한다.
      int count = 1; // 자기 자신을 포함하므로 1부터 시작
      // 현재 노드와 연결된 다른 노드들을 탐색
      for (int next : graph[current]) {
          // 이어져있는 것 중 연결된 것으로 간다. 트리는 다 이어져있지만 루프가 없으니까 가능한 것이다.
          if (next != parent) {
              count += dfs(next, current, graph);
          }
          // 끝에 닿아서, 1개와 연결되어 있는데 이 조건에 걸리지 않으면 재귀가 끝난다. ( 끝에 닿았다는 것이니까.)
      }
      return count;
  }

  int solution(int n, vector<vector<int>> wires) {
      int answer = n; // 최솟값을 찾기 위해 가장 큰 값(전체 개수)으로 초기화

      // 1. 트리(그래프) 구성
      // 번호가 1번부터 n번까지이므로 n+1 크기로 만듭니다.
      vector<vector<int>> graph(n + 1);
      for (const auto& wire : wires) {
          graph[wire[0]].push_back(wire[1]);
          graph[wire[1]].push_back(wire[0]);
      }

      // 2. 모든 전선을 하나씩 끊어보며 확인
      for (const auto& wire : wires) {
          int u = wire[0];
          int v = wire[1];
          // 이렇게 여러번 쓸 값은, for 문 안에 있더라도 변수로 할당을 해준다.

          // u에서 v로 가는 연결 임시 제거
          // 2중 벡터라서, idx를 벡터 내부의 한 벡터에 고정해야한다. ( erase, remove는 원래 1차원 벡터에 쓰는 거니까 )
          graph[u].erase(remove(graph[u].begin(), graph[u].end(), v), graph[u].end());
          // v에서 u로 가는 연결 임시 제거
          graph[v].erase(remove(graph[v].begin(), graph[v].end(), u), graph[v].end());
          // remove가 출력이 지우고 남은 끄트머리 공간의 주소임.
          // 그래서 erase의 앞에 둔다. 범위 선택해서 공간 지우도록.

          // 3. 한쪽 그룹의 송전탑 개수 세기
          // u번 노드에서 탐색 시작 (부모 노드는 없으므로 0 전달)
          int count_group1 = dfs(u, 0, graph);

          // 4. 반대쪽 그룹의 개수와 두 그룹의 차이 계산
          int count_group2 = n - count_group1;
          int diff = abs(count_group1 - count_group2);

          // 최솟값 갱신
          answer = min(answer, diff);

          // 5. 다음 확인을 위해 끊었던 간선 다시 복구
          graph[u].push_back(v);
          graph[v].push_back(u);
      }
      return answer;
  }
  ```
- 4차 시도 ( 0m / 25m 공부만 함. )
  - 이게… remove erase 이 조합 매번 헷갈리고 까먹어서 그냥 visited 방식으로 바꾼다.
  - 그냥 원래 방식에서 dfs에서 시작 노드를 단절시킬 부분으로 시작시키면 된다.
- 3차 시도 ( 35m / 5m )
  - 이산 수학에서 배운 인접 그래프의 개념을 떠올려서 데이터 전처리의 단서를 알아냈다.
  - 인접 그래프를 떠올렸는데, 알고리즘에서는 인접 리스트라는게 그것에 해당한다. ( 각 노드가 자신과 연결된 노드를 전부 기록해두는 것 )
  - 여전히 어렵다
    - 그래프 구성에도 시간이 오래 걸렸고 ( 결국 하긴 했으나 )
    - 그래프를 끊는 것에도 벙쪘고 못했다.
    - 그리고 DFS에 뭔 인자를 넣어야 하는 지도 모르겠다
  - 저녁에 피로하고, 악마가 연락으로 괴롭혀서 상태 급격히 나빠짐
  - 5m 정도 답의 로직만 다시 봤다.
- 2차 시도 ( 45m )
  - 결국 답을 봤다.
  - 그런데, 확실히 전에 했던 때보다는 눈에 익고, 이해가 더 잘 된다.
  - 2중 벡터가 나은 지, map이 나은 지를 잘 생각해봐야할 듯
  - 완전 탐색은 꼭 if로 케이스들 다 체크하는 것만 있는게 아니고, 그래프 구축하고 dfs로 끝까지 다 뒤지는 것도 포함된다.
  - dfs 안에서 for문을 돌리면 여러 분기로 파고 들어갈 수 있다.
  - 또한, 끝에 도달했는 지를 판단하는 매개변수는 current와 parent이다. 그래프 안에서 current에 해당하는 벡터를 꺼내서, 그 안의 다음 노드로 가려고 할 때, 그게 parent ( 이전에 거쳐온 노드 ) 와 중복이 되면 그때가 끝부분이라는 의미이다. 이전에 거쳐온 곳과 다음으로 갈 곳이 같다는 것은, 이제 중복이 안되게 다음으로 넘어갈 수 있는 부분이 없다는 것이다.
  - 또한 count를 1로 할당하고, return에서 count를 돌려주는 방식도 배웠다. 그냥 dfs 작동 자체가 노드를 하나 방문했다는 뜻이라 1을 할당하고, return에서는 for을 탈출했을 때, 그 이전에 실행한 dfs로 return을 통해 값을 넘기고, 넘기고 하면서 누적시키며 답이 나오는 구조이다. 제일 끝에서는 1을 돌려줄 것이고. 그래서 1에서 시작해서, 모든 경로에 대한 노드 갯수 합이 나오는 구조이다.
- 공부, 흡수 ( 60m )
  - 잘 공부했다 굳
- 1차 시도 ( 25m )
