# 타겟 넘버 : 레벨2, DFS

- 2026-11-10 : 3회차 도전 ( 40m 소요 ) → 짜긴 했는데 디버깅에서 어려움 겪음.
  - 코드는 다 똑바로 짰다. 단, 딱 2가지로 인해 오답으로 나왔다.
    - idx의 범위 1차이
    - a, b 갱신을 앞에서 한 것
  - 재귀는 결과를 보고 역추적하지 마라. cout로 보인 결과만으로는 절대 알 수가 없다.
    - 그냥 일단 인덱스 살짝 바꾸기
    - 갱신 코드 위치 바꾸기
    - 이거 경우의 수 따지며 해서 접근하자.
  - 실수
    ```cpp
    #include <string>
    #include <vector>
    #include <iostream>

    using namespace std;

    // 주어지는 숫자 갯수가 최대 20개임.
    // 2 ^ 20이 최대임. ( 그렇게 안 크다 ) -> 다 구하도록 dfs
    int dfs(int idx, int stake, int target, const vector<int>& numbers) {
        // int a = stake - numbers[idx];
        // int b = stake + numbers[idx];
        cout << a << " " << b << " " << stake << " " << numbers[idx] << " " << idx << " " << endl;
        // idx ++;
        if (idx == numbers.size()) { // idx가 끝까지 보게 하려면 numbers.size() - 1이 아니라 그냥 size임
            if (stake == target) { // 재귀에서는 idx가 1이 더 길어야 하는 듯.. 흐름 예시 보니까 그런 거 같다.
                return 1;
            } else {
                return 0;
            }
        }
        int a = stake - numbers[idx];
        int b = stake + numbers[idx]; // 얘들이 맨 앞에 있었어서, 마지막 stake가 반영이 안되어서 하나가 씹혔다고 한다.
        // 갱신하는건 뒤로 빼야할 듯
        return dfs(idx + 1, a, target, numbers) + dfs(idx + 1, b, target, numbers);
    }

    int solution(vector<int> numbers, int target) {
        int answer = 0;
        answer = dfs(0, 0, target, numbers);
        return answer;
    }
    ```
- 2026-07-22 : 2회차 도전 ( 25m 소요 ) → 맞추지는 못함, 그러나 전보다 훨씬 잘함.
  - 실수
    - Core dumped : 재귀를 멈추는 base case ( 종료 조건 )가 없을 때 뜨는 오류임
    - base 설정하는 법
      - idx를 끝까지 돈 뒤에 판단하도록 만든다.
      - 함수 자체가 int이다. 그래서 다 돈 뒤에 결과가 일치하지 않으면 return 0, 일치 하면 0이 되도록 한다.
      - 얘들은 결국, 그냥 끝까지 가봤을 때 0 또는 1이 된다는 느낌이다. 뭔가 트리처럼 결과물들이 구축되고 끝에 리프가 생긴다. 그리고 그 끝부분들을 기반으로 앞으로 쭉쭉 더하는 것이다.
      - 뭔가 순서가… 문제랑 반대같은데, 아무튼 원리가 그렇다. 끝부분에서 return이 뭔가 나오고, 그것에서 시작해서 앞으로 쭉 더해오는 느낌. 이해라기보다 느낌이다.
      - dfs 함수의 마지막에 return을 추가해서, main으로 빠지게 한다. return 쪽에서 재귀들을 갖고 더하거나 뺀다.
  - 생각
    - 그래도 전보다 재귀를 다루는 감각이 생겼다. 호출에 넣을 인자를 뭘로 해야할 지 라던가…
    - 인자는 다 맞게 넣었다. 원래 아예 몰랐는데.
    - 걍 한번 전에 답지를 봤으면, 기억이 감각적으로 나는 듯. 뚜렷이 안나도, 보면 그냥 가물가물하게 감으로.
    - 그러니까, 애초에 첫 시도에는 적당히 하는게 맞다.
    - 그냥 첫 시도에 30분 정도 찌끄리고, 틀린 이유 적어두고, 징징대면서 답지 보고 흡수하고 나서 나중에 1번 더 보는게 첫트에 3시간 붙잡고 대가리 쥐나는 것보다 훨씬 흡수가 잘 되고, 연속성도 안 무너진다.
    - 안되면 걍 바로 놔라. 답지 보고, 틀린 이유만 적어라. 그리고 다음 날에는 다른 문제 해라. 나중에 틀렸던거 다시 봐라.
    - 진짜, 걍 형태 암기다.
      - 재귀에 대한 개념적 이해 → 걍 문제보고 ‘재귀 써야겠네’ 이 정도 연상만 될 정도면 충분하다.
      - 그러고 나면, 외운걸 기반으로 이해가 되는거다. 외우는게 우선이다. 영어랑 똑같다. 단어 외우고 아는 단어 늘면 문장의 문법이 눈에 잘 들어오듯이 말이다. 근데 걍 외워서 용이해진 것이다. 단어 모르면 단어부터 외워야 한다.
- 2026-05-04 : 문제 슥 보고 답지 한번 보기
  - 답지 구축 ( 구글에서 답지 한번 보고 스스로 해봄 )
    ```cpp
    #include <string>
    #include <vector>

    using namespace std;

    int count = 0;

    void dfs(int idx, int res, vector<int> numbers, int target) {
        if (idx == numbers.size()) {
            if (res == target) {
                count++;
            }
        } else {
            dfs(idx + 1, res + numbers[idx], numbers, target);
            dfs(idx + 1, res - numbers[idx], numbers, target);
        }
    }

    int solution(vector<int> numbers, int target) {
        int answer = 0;
        dfs(0, 0, numbers, target);
        answer = count;
        return answer;
    }
    ```
  - 생각들
    - DFS
      - 기본적으로 재귀를 쓴다. ( 스택도 가능 )
      - 그리고 함수에 넘겨주는 것은 ‘dfs(배열, 위치, 방문 여부 배열 or 계산을 누적해야 하는 것)’ 이다.
      - dfs 내부에서 재귀로 넘겨주는 것은 dfs(배열, 다음 위치, 방문 여부 배열 or 계산을 누적해야 하는 것)
      - 사실 문제마다 다 다르다.
      - 이 함수가 다음 층으로 내려갈 때, 다음의 나에게 꼭 전달해줘야 하는 기억의 조각들
      - 이 감각을 익히자.
      - 그냥 메인에서 부를 때는 basecase에서 시작하는 것임 ( 이산수학에서 배웠듯이 )
      - 즉, 0을 넣으면 도미노처럼 알아서 됨.
      - 이상한 걸 봐서 휘둘렸는데, 함수 선언 solution 밖에 해도 된다.
  - 의문점
    ```cpp
    #include <string>
    #include <vector>

    using namespace std;

    int count = 0;

    void dfs(int idx, int res, vector<int> numbers, int target) {
        if (idx == numbers.size()) {
            if (res == target) {
                count++;
            }
            // 여기서 if (idx == numbers.size() && res == target) 이건
            // 왜 안됐던거지? 뭔 차이지?
            // 중요 : 그냥 제일 위의 if() 내부에 넣은 것이 종료 조건 ( base case )이다.
            // 그냥 저걸 만족하면 count ++ 하고 그 재귀는 끝이 나는 것이다. 그런 구조이다.
        } else {
            dfs(idx + 1, res + numbers[idx], numbers, target);
            dfs(idx + 1, res - numbers[idx], numbers, target);
        }
    }

    int solution(vector<int> numbers, int target) {
        int answer = 0;
        dfs(0, 0, numbers, target);
        answer = count;
        return answer;
    }
    ```
