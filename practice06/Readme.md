
# Lab 09
## 실습 내용
### **적외선 컨트롤러**
#### **ir_rx module**
##### 1M Clock = 1 us Reference Time 
: 1us를 기준으로 하는 clock 생성
##### Sequential Rx Bits
: reset 0을 받을 때 초기화 되며 clk_1M의 값이 1일때 seq_rx[0]에 리모컨에서 준 신호를 저장하고 seq_rx[1]에는 바로 전에 받은 신호를 저장 
##### State Machine
: state를 총 4개로 구성. 처음에 IDLE로 시작하며 9.5ms(9000us, 오차율 10%)정도 1, 4.5ms(4000us, 오차율 10%)정도 0을 받으면 DATACODE로 state가 넘어가서 들어오는 신호를 count 하기 시작하고 신호가 32개가 들어오면 COMPLETE로 state가 다시 넘어가서 초기화가 이루어진다.
##### 32bit Custom & Data Code
: 처음에 leader code를 받았다면 그 이후에 들어오는 신호를 저장
#### **top module**
: wire 이름을 설정하여 block diagram에 맞도록 module을 이음.

### ** 동작원리 **
처음에 신호를 받으면 차례로 1이 약 9.5ms, 0이 약 4.5ms 정도 들어오는 leader code 부분이 있다. 이 부분이 끝나면 그제서야 들어온 신호를 저장하는 seq_rx나 얼만큼 신호가 들어왔는지 세주는 cnt32가 작동하기 시작한다. 그 다음에 들어오는 16bit는 custom code이며 리모컨과 기기가 연동되도록 만들어주는 역할을 한다. 그다음 16bit는 data code인데 8bit data code를 받으면 나머지 8bit는 상보적인 데이터이다. leader code가 끝나고 신호가 들어오면 cnt32가 1bit 씩 세주어 32개가 되면 다시 초기화시킨다.  들어온 신호는 seq_rx[0]에 저장되며 그 순간부터 1이면 cnt_h가 늘어나고 0이면 cnt_l가 늘어난다. 그렇게 신호를 저장하여 총 들어온 신호가 끝나면다시 초기화되어 또 신호를 받을수 있는 상태로 돌아간다. 저장된 data는 다음 신호가 들어올때 o_data에 표시된다.

<!--stackedit_data:
eyJoaXN0b3J5IjpbNTAyMjkyNTYyLC0xMDMzNzk5NjU1XX0=
-->