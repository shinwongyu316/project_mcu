# project_mcu

1장 ATmega128

특징 : RISC 구조, 주소지정 모드(addressing mode)제공, ISP기능 등

 - RISC : 명령어 수를 줄여서 한 명령을 빠르게 수행하는 CPU 설계방식

 - ISP : MCU를 보드에 장착한 상태에서 SPI 등을 통해 프로그램을 바로 다운/수정하는 방식

 - SPI : 클럭을 기준으로 마스터-슬레이브가 MOSI/MISO로 고속데이터를 주고받는 동기 직렬 통신방식
   
 - 오실레이터 : 주기적인 전자 신호를 생성하는 필수 구성 요소로 DC 신호를 주기적인 AC 신호로 변환한다. 모든 마이크로컨트롤러와 마이크로프로세서는 기능을 수행하기 위해 클록 신호를 설정하는 오실레이터가 필요하다.

 - PWM : 펄스 폭 변조(Pulse Width Modulation)의 약자로 Pulse-사각파, Width-폭, Modulation-변조로 사각파의 폭을 변화시키는 것이다. Duty Ratio를 조절하는것으로 자세한 설명은 아래 링크를 참조한다.
   https://blog.naver.com/lagrange0115/222345576071
   
<img width="786" height="662" alt="image" src="https://github.com/user-attachments/assets/0744db93-1781-48b9-bccd-c3e30dded9c6" />
<img width="826" height="453" alt="image" src="https://github.com/user-attachments/assets/2f39030d-f122-4b40-9ee2-80ca2da0c7b3" />
<img width="822" height="435" alt="image" src="https://github.com/user-attachments/assets/42cc5240-e8c9-4b45-9bce-d7500e657df6" />
<img width="833" height="413" alt="image" src="https://github.com/user-attachments/assets/afdf5d31-7433-4fac-9f62-4fd237551e38" />

 - 내장 메모리
    - 플래쉬 메모리, EEPROM SRAM
 - I/O
   - 2개의 8bit 타이머/카운터 + 확장 16bit 타이머/카운터
   - 독립적인 오실레이터를 갖는 실시간 카운터(RTC)
   - 6개의 PWM채널
   - 8채널 10bit ADC
   - 2개의 프로그램가능한 직렬 USARTs
   - SPI, TWI 직렬 인터페이스
   - 내장된 오실레이터로 구현된 프로그램 가능한 워치독 타이머
   - 아날로그 비교기 내장
   - 53개의 프로그램 가능한 입출력 I/O
- 기타
   - 8개의 외부 인터럽트, 27개의 내부 인터럽트
   - 온칩 디버깅이 가능한 JTAG

<img width="868" height="638" alt="image" src="https://github.com/user-attachments/assets/858c2f29-4d42-4ab0-8b21-287608194600" />

1. CPU
   - ALU : 산술,논리,비트 연산 수행, 32개의 범용레지스터와 직접 연결
   - General Purpose Working Register : AVR의 고성능 RISC 명령을 수행하는데 최적화 되어있는 레지스터
   - 상태레지스터(SREG) : 8bit -> <7bit>I(Global Interrupt Enable) <6bit>T(BitCopyStorage) <5bit>H(Half Carry Flag) <4bit>S(Sign Bit) <3bit>V(two'scomplementoverflowbit) <2bit>N(Negative Flag) <1bit>Z(Zero Flag) <0bit>C(Carry Flag)
   - stack pointer : LIFO구조이며, 마이크로컨트롤러응용에서 서브루틴이나 인터럽트발생 시에 복귀되는 주소를 임시로 기억하기 위해서 사용됨, 스택은 내부SRAM 영역 내에 존재
   - RAM 페이지 선택레지스터 : ATmega128 내부의 128K 바이트 플래시 프로그램 메모리에서 64K 바이트 단위로 구분되는 2개의 페이지 중에서 어는 것을 사용할지를 지정하는 역할.
    - RAMPZ=0 : 프로그램 메모리는0 페이지선택(0x0000~0x7FFF 영역)
    - RAMPZ=1 : 프로그램 메모리는1 페이지선택(0x8000~0xFFFF 영역)
     
2. 메모리
   - program memory : 내부 128KB의 플래시메모리(ISP 플래시메모리)로 SPI 통신방식을 이용한 ISP 기능이나 JTAG 에뮬레이터, 병렬 프로그래밍방식을 통해 사용자 프로그램을 쉽게 기록할 수 있음.
   - data memory :
   - EEPROM : 쓰기/읽기는 I/O 레지스터를 사용하여 주소를 지정하는 방법으로 액세스가능
     - EEPROM의 제어
      - EEAR : EEPROM 주소 레지스터
      - EEDR : EEPROM 데이터 레지스터
      - EECR : EEPROM 제어 레지스터
     
3. 기타
   - 시스템 클럭
   - 시스템 리셋
   - 메모리보호 및 부트로더
   - 전원관리 및 슬립모드
   - 워치독 타이머


AVR - C


Atmega128의 I/O port는 <iom128.h>에 저장 - SRAM

0x20~0x5F : 64개의 I/O 레지스터
 - _SFR_IO8, _SFR_IO16
 - #define PINB  _SFR_IO8(0x16), #define DDRB  _SFR_IO8(0x17), #define PORTB  _SFR_IO8(0x18)

0x60~0xFF : 160개의 확장 I/O 레지스터
 - _SFR_MEM8, _SFR_MEM16

-> 0x16, 0x17, 0x18은 avr에서 지정해 놓은 값이며, 임의로 수정할 수 없다. 또한 A~F 각 포트안에 있는 핀 0-7은 각각의 PIN(입력 레지스터), DDR(입출력 방향을 설정하는 레지스터), PORT(출력 레지스터)를 갖는다.

<img width="928" height="500" alt="image" src="https://github.com/user-attachments/assets/b79ba3a7-12fc-4189-9369-4dec4bf2ea07" />





