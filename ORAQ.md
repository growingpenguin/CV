# OraQ, Selene TR-1
클라우드 기반의 DICOM (PACS) 미디어 관리도구 및 뷰어

  - 작성 언어
    - C++
    - Flex (Actionscript 3.0)
    - Pixel Bender
    - MS-SQL
  - 특징
    - 클라우드 어플리케이션 (웹 페이지)
    - 웹과 C++ 간 연동 필요  (DICOM 라이브러리가 전부 C++ 기반)
    - GPU Programming 구현 필요
      - 이미지 한 장에 수백 MB ~ 수 GB 를 상회
      - 이를 20 프레임 단위로 이펙트 효과를 줄 수 있어야 함
    - 서버 전체 단위의 임계영역 제어 필요
  - 초기 매뉴얼: [assets/manuals/oraq.pdf](assets/manuals/oraq.pdf)

## 1. Hardcore Difficulty
OraQ (일본에서는 Selene TR-1) 는 제가 대학교를 졸업하고나서 처음 개발했던 상용 프로젝트입니다. 그리고 동시에, 제가 개발했던 모든 상용 프로젝트들을 통틀어서 가장 난이도가 높았으며, 기한 또한 제일 급박하였습니다.

당시 회사 (스마트케어웍스) 는 이미 5 년 전에 이 프로젝트에 대한 계획을 세워두고 있었으되, 이를 개발해 줄 수 있는 개발자가 없어 하염없이 세월만 지새우고 있었습니다. 그나마 웹 개발자를 구하여 프로젝트를 진행하였으되, 개발에 실패하고 해당 개발자는 속칭 빤쓰런을 하게 됩니다. 그리고 이 프로젝트의 만료 기일은 어느덧 4 달 앞으로 성큼 다가와버린 상태였죠.

저는 이런 속사정 모르고, 그저 간단한 웹 사이트나 만드는 프로젝트 정도로만 이야기를 들은채로, 프리랜서 계약을 하게 됩니다. 그리고 얼마 지나지 않아, 제가 만들어야 할 프로젝트의 실체를 알게 됩니다.

  - DICOM 미디어는 이미지 한 장에 수백 MB ~ 수 GB 에 이른다.
  - 의사는 이러한 이미지 수백장에 실시간으로 블러 효과 등을 주어가며 관찰한다.
    - 마우스 (휠) 스크롤시 끊김없이 실시간 변화가 가능해야 함
    - 이미지 프로세싱으로 인해 끊김 현상이 있어서는 안 됨
  - 해당 클라우드 시스템은 동시에 여러명이 쓰며, 이들간에 동기화가 이루진다.
  - 사용자 컴퓨터의 CD 드라이브로부터 DICOM 미디어 정보를 불러와 PACS 에 기록할 수 있다.
  - 이 모든 것이 웹 브라우저에서 가능해야 한다.

그리고 DICOM 이나 PACS 에 연동을 위해 필요한 외부 라이브러리는 모두 C++ 로 제작되어 있었으며, 서버에서는 클라이언트들과 실시간으로 통신하며 상당한 수준의 임계영역 제어가 이루어져야 했습니다. 게다가 당시에는 웹 브라우저에서 GPU 프로그래밍을 할 수 있던 시절도 아니었고, Web Socket 도 이제 막 걸음마를 떼기 시작한 단계였습니다.

따라서 OraQ 는 당시의 웹 브라우저 기술이나, 전통적인 웹 서버 기술스택 (JSP, PHP 등) 로 만들 수 있는 성질의 것이 아니었습니다. 이를 인지한 순간, 프로젝트 도중에 도망친 전임 웹 개발자가 나름 합리적인 판단을 했다고 느껴지기까지 했습니다.

## 2. New Challenge
프로젝트의 난이도가 워낙 높은지라 자칫하면 실패할 수도 있겠다 생각이든 저는, 이왕 이렇게 된 거 과감한 도전과 실험이라도 해보면 뭐라도 배우고 얻어가는 게 있겠지라는 심정으로, 기존의 모든 전통적인 개발스택을 포기하고 과감히 새로운 시도를 하게 됩니다.

먼저 클라우드 서버 전체를 C++ 로 만들기로 합니다. 그리하면 DICOM 미디어 및 PACS 와의 원활한 연동이 가능해질 것이고, 중대 임계영역 제어 또한 직접 할 수 있게 됩니다. 더불어 프론트와의 실시간 연동 역시 소켓 프로그래밍을 통해 직접 구현하면, 방대한 데이터 전송량을 필요에 따라 적절히 제어하고 최적화할 수 있게 됩니다.

프론트 프로그램은 Flex 로 만들기로 합니다. Flex 는 Flash Player 기반의 프레임워크로써, 당시에는 Flash 만이 유일하게 웹 브라우저에서 GPU 프로그래밍을 할 수 있었습니다. 더불어 카메라나 CD 드라이브같은 하드웨어 장비를 제어할 수도 있었습니다. 게다가 웹 브라우저에서 커스텀 소켓 프로그래밍이 가능한 유일한 언어이기도 했습니다. 지금이야 웹 브라우저에서 퇴출될 위기에 몰린 Flex (Flash) 지만, 당시에는 OraQ 를 구현할 수 있는 유일한 도구였습니다.

마지막으로 철저한 아키텍처 설계와 정책 수립을 제 1 의 원칙으로 삼았습니다. 족보에도 없는 전혀 새로운 방법으로 개발하는 클라우드 시스템인만큼, 그 어떤 노하우나 도움을 구할 수도 없었기 때문입니다. 어중간한 하드코딩은 프로젝트를 파멸로 치닫게 할 것이며, 오로지 철두철미한 계획과 원칙을 지켜나가는 완고함만이 프로젝트를 성공으로 이끌어가는 유일한 열쇠라고, 저는 그리 믿고 행동하였습니다.

## 3. Later Story
철저한 아키텍처 설계와 원칙을 지켜나가는 완고함, 그리고 기존의 개발스택을 과감히 버리고 새로운 방식으로의 개발을 시도한 끝에, OraQ 는 실로 4 개월만에 완성되어 정상 출시할 수 있었습니다. 게다가 설계 과정에서 만들었던 아키텍처와 각종 문서들이 매뉴얼을 좋아하는 일본 사람들의 취향에 맞았는지, 업계에서는 소소한 파란을 일으키어, 데모 단계에서부터 일본의 고베병원 등이 OraQ 솔루션을 선 구매하는 기적이 일어나기까지 했습니다

> 출시가 되지 않은 제품을 선 계약하는 것은 일본에서 매우 드문 일이라고 합니다.

다만, 이후 회사의 행보는 다소 이해하기 어려웠습니다. 회사는 우선 비용을 줄이기 위하여 무던히 노력하였고, 그 일환으로써 저와의 계약을 연장하지 않았습니다. 그 결과 일본으로부터 빗발치는 패치 및 유지보수 요구에 대응하지 못하여, OraQ 라는 프로젝트는 적자는 아니되 큰 성공을 거둔것도 아닌, 어중간한 반쪽짜리 프로젝트로 끝나게 됩니다. 3 개 병원을 끝으로 추가 구매자가 나타나지 않았던 것입니다.

> 제가 의사나 의료계 종사자도 아닌데, DICOM 미디어에 대한 현장의 세세한 요구사항까지 어찌 알겠습니가? OraQ 는 한정된 기간 내에 주어진 스펙과 기능 구현에 충실한 제품이었을지는 몰라도, 사용자들의 가려운 곳을 긁어주는 그런 노하우를 가진 그런 프로그램은 아니었습니다.
> 
> 따라서 출시 후 유지보수의 요구가 빗발칠 것은 뻔히 보였는데, 회사는 어쩌다가 소탐대실의 오판을 해버린 것인지 지금도 잘 이해가 안 갑니다.

다만, 회사는 막판의 오판으로 인하여 대박의 기회를 놓쳤어도, 저는 그 기회를 놓치지 않았습니다. 프로젝트가 대성할 수도 있었다는 아쉬움은 뒤로 한 채, 4 개월 간에 거두었던 성과를 가지고 다른 회사의 문을 두드렸습니다. 그리고 바로 이 성과 덕분에, 대졸 신입사원은 그 어떤 회사를 들어가도, 시작부터 코어 아키텍처를 설계하고 핵심 알고리즘을 연구하고 개발할 수 있는 기회를 부여받게 됩니다.

지금도 가끔 생각합니다. 대학교를 졸업하고 첫 경력으로 맡은 프로젝트가 OraQ 인데, 이런 기회가 생기는 것은 개발자들 중에서도 정말 극소한 케이스로, 매우 운이 좋았더라고 말입니다. 만일 제가 대학교를 졸업하고나서 처음 맞이한 프로젝트가 OraQ 가 아니라, 평범한 회사에 다니면서 평범하게 신입 개발자로 경력을 시작했더라면, 과연 이후로도 그런 파격과 기회들이 주어지기나 했을까요?