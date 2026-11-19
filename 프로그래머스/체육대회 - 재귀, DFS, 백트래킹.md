# 2 / 체육대회 : 재귀, DFS, 백트래킹 ( 이것도 공부좀 하자. 재귀가 아킬래스건인데, 2번으로 늘 나오는 듯 )

- 공부
  - 재귀에 대한 분석 → 좆같이 머리에 안 붙어서 하나하나 뜯어서 기록하자.
    - 함수의 입력에 “현재 idx, 순환을 돌 때 도달하는 마지막 지점의 idx, 참조할 2차원 배열” 이렇게 있다.
    - base case가 마지막 지점에서 나타난다. 그런데 마트료시카같이 올라오는게 아니라 max_score라는 외부 변수를 갱신시키고 함수를 종료시켜버린다. 함수 자체도 void이다. 외부 변수를 변화시키는게 목적이니까.
    - 하향식으로 결론에서 끝내는 방식
    - 저번에 한 겹치며 올라오는건 상향식이라고 함.
    - 얘는 함수가 int 혹은 bool이었는 듯
    - 함수의 작동을 완전히 이해하려하기 보다, 다음으로 뭘 넘겨줄 지에 집중하자.
  - 무조건 학생 수가 종목 수보다 더 많음.
    - 그래서, 종목 수를 기준으로 탐색의 깊이 ( 트리의 depth, idx ) 를 정해야 한다고 함. 더 적은 쪽을 기준으로 해야 간결하고 비효율이 적다고 한다.
    - idx가 곧 함수로 넘겨줘서 하나씩 올리며 파고 들어갈 대상의 idx에 해당한다.
  - ability[i][idx] 이러면 idx에 열이 고정되고, 반대는 행이 고정된다.
    - ability[행][열] : 이거 기억하자.
    - for (int i = 0; i < num; i++) { if (!visited[i]) { ~~ ability[i][sport_idx] ~~ // 이런 식으로 하면 2차원 배열에서 열에 고정하고 행을 탐색 가능하다. } }
- 정답 코드
  ```cpp
  #include <string>
  #include <vector>
  #include <algorithm>

  using namespace std;

  int max_score = 0;
  int num_students;
  int num_sports;
  vector<bool> visited;
  // 한 열을 탐색할 때, 이미 방문한 학생을
  // 이런 식으로 배제할 수 있다.
  // 저 dfs 안에서 재귀로 파고 들어간 이후에 false로
  // 초기화 시켜서 다음 dfs에서 다시 돌아가게 하는게 요령인 듯

  void dfs(int sport_idx, int current_sum, const vector<vector<int>>& ability) {
      // 모든 종목에 대한 대표 선발이 완료된 경우
      if (sport_idx == num_sports) {
          max_score = max(max_score, current_sum);
          return;
      }

      // 각 종목마다 모든 학생을 확인하며 대표를 선발
      for (int i = 0; i < num_students; ++i) {
          if (!visited[i]) {
              visited[i] = true; // 학생 선택 (다른 종목에서 선택하지 못하도록 체크)
              dfs(sport_idx + 1, current_sum + ability[i][sport_idx], ability);
              visited[i] = false; // 탐색 종료 후 선택 해제 (백트래킹)
          }
      }
  }

  int solution(vector<vector<int>> ability) {
      num_students = ability.size();
      num_sports = ability[0].size();
      // 이런 식으로 미리 각 길이를 저장해둔다.
      // 열의 길이를 뽑을 때는 [0] 을 하고 ( 아무거나 뽑아서 ) .size를 하면 된다.

      // 학생 수만큼 방문 체크 배열을 초기화합니다.
      visited.assign(num_students, false);
      max_score = 0;

      // 0번 종목, 누적 능력치 합 0부터 탐색을 시작합니다.
      dfs(0, 0, ability);
      return max_score;
  }
  ```
- 공부 ( 35m )
- 1차 시도 ( 22m )
- 파악
  - 0 ≤ ability[i][j] ≤ 10,000
    - 이 조건은 각 내용물의 int 한도가 저 정도라는 거였다.
  - 1 ≤ ability의 열의 길이 = 종목 수 ≤ ability의 행의 길이
    - 조건 잘 읽어라. 열의 길이도 나와있었다.
    - 백트래킹, DFS를 이용한다.
    - 유망하지 않은 방향은 그냥 안 보고 재끼는 것 ( A를 포함한 가지, A를 포함하지 않은 가지 이렇게 2가지 세계선 )
