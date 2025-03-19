# 리눅스와 하드웨어
> 하드 디스크 드라이브
> - 파티션 변경이나 설정, 스왑 설정 등 변경할 일이 발생할 수 있음
> - 하드 디스크 인터페이스에 따라 파일명이 달라짐
> -     IDE 디스크 : /dev/hda, /dev/hdb 등
> -     SCSI, S-ATA, USB 메모리, SSD : /dev/sda, /dev/sdb 등

## RAID(Redundant Array of Independent Disk)
- 여러 개의 하드 디스크가 있을 때 동일한 데이터를 다른 위치에 중복해서 저장하는 방법
- 데이터를 여러 개의 디스크에 저장하여 입출력 작업이 균형을 이루게 되어 전체적인 성능을 향상시킴
- 백업을 가능하게 하고 안정적인 데이터의 보존과 유지기능, 속도 향상 등에 사용

RAID에서 사용하는 기술
1) 스트라이핑(Striping)
   - 연속된 데이터를 여러 개의 디스크에 라운드로빈 방식으로 기록하는 기술
2) 미러링(Mirroring, 결함 허용)
   - 디스크에 에러 발생 시 데이터의 손실을 막기 위해, 하나 이상의 장치에 중복 저장하는 기술

### RAID의 종류
1) RAID-0

![RAID0](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID0.jpg)
- 스트라이핑 기술을 사용하여 빠른 입출력 속도를 제공
- 구성된 디스크 중 오류가 발생하면 데이터 복구가 불가능
2) RAID-1

![RAID1](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID1.jpg)
- 미러링 기술을 사용하여 두 개의 디스크에 데이터를 동일하게 기록
- 읽기 성능은 향상되나 쓰기 성능은 단일 디스크와 같음
- 중복 저장으로 인한 디스크 낭비가 50%
3) RAID-2

![RAID2](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID2.jpg)
- 디스크들은 스트라이핑 기술을 사용하여 구성
- 디스크들의 에러를 감지하고 수정하기 위해 ECC(Error Check & Correction) 정보를 사용
4) RAID-3

![RAID3](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID3.jpg)
- 스트라이핑 기술을 사용하며 디스크를 구성
- 패러티 정보를 저장하기 위해 별도로 하나의 디스크를 사용
- 입출력 작업이 동시에 모든 디스크에 대해 이루어지므로 입출력을 겹치게 할 수 없다
- 보통 대형 레코드가 많은 시스템에서 사용
- 데이터 입출력은 바이트 단위로 이루어짐짐
5) RAID-4
- RAID-3과 동일하나 데이터 입출력이 블록 단위로 이루어짐
- 단일 디스크로부터 레코드를 읽을 수 있음
- 입출력의 중첩이 불가능하고 시스템에 병목현상이 발생 할 수 있음
6) RAID-5

![RAID5](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID5.jpg)
- 패리티 정보를 이용하여 하나의 디스크가 고장이 발생할 경우에도 사용이 가능한 구성 방식
- 최소 3개의 디스크로 구성
- 패리티 정보는 별도의 디스크를 사용하지 않고 구성된 디스크에 분산하여 저장
- 패리티 공간 = 1/n : 디스크의 개수를 늘릴수록 저장 공간의 효율성이 높아짐
- 쓰기 작업이 많지 않은 다중 사용자 시스템에 적합
7) RAID-6

![RAID6](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID6.jpg)
- 2차 패리티 구성 : 2개의 디스크 오류에도 데이터를 읽을 수 있음
- 최소 4개의 디스크로 구성
- 패리티 공간 = 2/n
- 처리 속도는 떨어지나 데이터에 대한 신뢰도 향상
8) RAID 0+1

![RAID01](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID01.jpg)
- 디스크 n/2개를 RAID-0의 스트라이핑 기술로 구성하고, 그것을 RAID-1의 미러링 기술로 구성
- 최소 4개의 디스크 필요
9) RAID-10

![RAID10](https://github.com/dee021/TIL/blob/linuxmaster/Linux/img/RAID10.jpg)
- 디스크 2개를 RAID-1의 미러링 기술로 구성하고, 그것을 스트라이핑하는 방식

10) RAID-53
- RAID-3 방식에 별로도 스트라이프 어레이(array)를 구성하는 방식
- 높은 성능을 제공하나, 구성 비용이 많이 든다.

## LVM(Logical Volume Manager)
리눅스 설치 혹은 하드 디스크 추가 시 파티션을 분할하고 공간을 할당한다. 이 때 공간의 크기는 고정되어 변경이나 용량 증설이 어렵다.<br> 
LVM으로 사용 중인 파티션의 크기를 줄이거나 늘릴 수 있다.

<b>물리적 볼륨(PV; Physical Volume)</b><br>
실제 디스크에 물리적으로 분할한 파티션
<br><b>물리적 확장(PE; Physical Extent)</b><br>
PV에서 나누어 사용하는 일종의 블록 영역이자 LVM에서 구성되는 단위<br>보통 1PE = 4MB 정도
<br><b>볼륨 그룹(VG; Volume Group)</b><br>
물리적 볼륨이 모여 생성하는 덩어리 = PE가 모여 생성되는 하나의 덩어리<br>
<b>논리적 볼륨(LV; Logical Volume)</b><br>
VG에서 사용자가 필요한 만큼 할당하여 만들어지는 공간<br>물리적 디스크에서 분할하여 사용하는 파티션
