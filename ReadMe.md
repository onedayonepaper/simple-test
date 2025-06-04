1. 디렉터리 구조
text
복사
편집
simple-test/
├── README.md        # 실행 방법 메모
├── main.cpp         # 오늘 작성할 테스트 코드
└── .gitignore       # 선택 – 빌드 산출물 무시
2. 예시 코드 main.cpp
cpp
복사
편집
#include <iostream>
#include <array>

/*
 *  Wi-Fi RSSI(신호 세기)를 품질(0~100%)로 단순 변환하는 함수.
 *  나중에 ESP32 코드에서도 그대로 쓰고 싶다면
 *  헤더만 빼서  calcSignalQuality.hpp  로 분리해 두면 좋습니다.
 */
int calcSignalQuality(int rssi)
{
    if (rssi <= -100) return 0;      // 거의 끊김
    if (rssi >=  -50) return 100;    // 최고 품질
    return 2 * (rssi + 100);         // 선형 보간
}

int main()
{
    std::array<int,5> testValues{ -100, -85, -70, -55, -40 };

    std::cout << "=== RSSI → Quality 테스트 ===\n";
    for (int r : testValues)
    {
        std::cout << "RSSI " << r << " dBm  →  "
                  << calcSignalQuality(r) << " %\n";
    }
    return 0;
}
코드 포인트
플랫폼 의존 코드 없음 – Arduino.h 같은 헤더를 쓰지 않았으므로 Mac 로컬 g++만 있어도 빌드됩니다.
(ESP32용으로 갈 때는 #include <Arduino.h>를 추가하고 타입만 약간 맞춰주면 끝!)

calcSignalQuality() 같이 순수 로직 함수들은 플랫폼과 무관하게 먼저 검증할 수 있습니다.

3. 빌드 & 실행
Mac(Clang++) 기준:

bash
복사
편집
cd simple-test
clang++ -std=c++17 -Wall -O2 -o test-rssi main.cpp
./test-rssi
출력 예:

text
복사
편집
=== RSSI → Quality 테스트 ===
RSSI -100 dBm  →  0 %
RSSI -85 dBm   →  30 %
RSSI -70 dBm   →  60 %
RSSI -55 dBm   →  90 %
RSSI -40 dBm   →  100 %
이렇게 원하는 결과가 나오면 로직은 정상이라고 볼 수 있습니다.

