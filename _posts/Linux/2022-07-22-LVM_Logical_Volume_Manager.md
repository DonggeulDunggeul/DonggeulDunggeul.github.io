---
title:  "[Linux] LVM(Logical Volume Manager)"
excerpt: "LVM(Logical Volume Manager)"
author: 동글

categories:
  - Linux
tags:
  - [Linux]

toc: true
toc_sticky: true
 
date: 2022-07-22
last_modified_at: 2022-07-22
---

VMware - Centos7  

> [참고](https://tech.cloud.nongshim.co.kr/2018/11/23/lvmlogical-volume-manager-1-%EA%B0%9C%EB%85%90/)

## LVM(Logical Volume Manager)  
파티션을 논리적인 개념인 볼륨으로 나눠서 더 유동적으로 디스크의 용량을 관리합니다.

![LVM](https://user-images.githubusercontent.com/109357459/180460906-59d4ce25-0cea-43a7-9487-86266e9acbff.png)



&nbsp;  

### 1. PV, PE

![PV,PE](https://user-images.githubusercontent.com/109357459/180461364-ea074563-297d-4227-a723-c04b8a9cbb1e.png)  

**1.1 PV(Physical Volume)**

>LVM에서 블록 장치를 사용하려면 PV로 초기화를 해야함 즉, 블록 장치 전체 또는 그 블록 장치를 이루고 있는 파티션들을 LVM에서 사용할 수 있게 변환한 것 예를 들어 /dev/sda1, /dev/sda2들을 LVM으로 쓰기위해 PV라는 형식으로 변환한 것 PV는 일정한 크기의 PE(Pysyical Extent)들로 구성  
>
>_블록 장치 : 블록 단위로 접근하는 스토리지. 예를 들어 대용량 하드 디스크_  


**1.2 PE(Physical Extent)**

>PV를 구성하는 일정한 크기의 블록으로 LVM2에서의 기본크기는 4MB, LV(Logical Volume)의 LE(Logical Extent)들과 1:1로 맵핑, 그렇기에 항상 PE와 LE의 크기는 동일, 즉, 아래 그림과 같은 모습 블록 장치(물리적 디스크)의 파티션들을 PV들로 초기화 시킨모습 각각의 PV들은 동일한 크기의 PE들로 구성

&nbsp;  

![VG](https://user-images.githubusercontent.com/109357459/180461395-7382be3d-8c13-479d-9e25-de50d7d17b34.png)    

&nbsp;  


### 2. VG

**2.1 VG(Volume Group)**

>PV들의 집합으로 LV를 할당할 수 있는 공간. 즉, PV들로 VG를 생성하는 과정은 LV로 할당할 수 있는 디스크 공간의 풀(Pool)을 생성하는것, 사용자는 VG안에서 원하는데로 공간을 쪼개서 LV로 만들 수 있음, 아래 그림과 같이 위에서만든 PV들을 하나의 VG1로 묶은 모습

&nbsp;  


### 3. LV, LE
  
**3.1 LV(Logical Volume)**

>사용자가 최종적으로 다루게되는 논리적인 스토리지, 위에서도 언급했지만, LV를 구성하는 LE들이 PV의 PE들과 맵핑하면서 존재

**3.2 LE(Logical Extent)**

>LV를 구성하는 일정한 크기의 블록으로 LVM2에서 기본크기는 4MB 위에서 언급했지만, 항상 PE와 LE의 크기는 동일  

&nbsp;  


물리적 볼륨을 삭제해도 논리적 볼륨은 살아있으므로 물리적볼륨 삭제 시 데이터는 보존 됩니다.

/boot 볼륨은 LVM이 인식하지 못하므로 /boot 물리적 볼륨 삭제 시 데이터가 삭제 됩니다.   ~~reboot시 리눅스 망가진거 확인 가능 합니다.^^~~  


&nbsp;  


## LVM 확장/용량 추가  

```
// 기존 파티션 확인
df -Th
```

```
// 추가된 디스크로 새 파티션 생성
fdisk /dev/sda

// n -> 파티션을 나누겠다
// p -> 기본 파티션으로 하겠다.
// 1 -> 파티션 번호
// w -> 저장
```  

![sda (1)](https://user-images.githubusercontent.com/109357459/180462770-503d7f68-8c59-47a7-a611-bf1bfb54b449.png)  

```
// 현재 잡혀있는 PV 확인
pvscan
```

```
// 새로만든 파티션을 PV로 생성
pvcreate /dev/sda1
```

```
// 현재 잡혀있는 PV 확인
pvscan
```

```
// 생성한 PV(Physical Volume)를 기존 VG(Volume Group)에 추가
vgextend VolGroup /dev/sda1
```

```
// PV(Physical Volume)확인하여 VG(Volume Group)에 추가 되었는지 확인
pvscan
```

```
// 새로 추가한 PV(Physical Volume)를 확인하여 용량 확인
pvdisplay /dev/sda1
```

```
// 늘리고자 하는 LV(Logical Volume)을 확장 
lvextend /dev/VolGroup/lv_root -l +2558
// 뒤에 숫자는 추가할 용량 byte
// VolGroup(VG name), lv_root(PV name)은 pvdisplay /dev/sda1 명령어를 통해 확인 가능
```

```
// 파티션 확인하면 아직 늘어나지 않은 것을 확인할 수 있음
df -Th
```

```
// 파일시스템 리사이징
// etx4
resize2fs /dev/VolGroup/lv_root

// xfs
xfs_growfs /dev/VolGroup/lv_root
```

```
// 파티션 확인하면 늘어난 것 확인 가능
df -Th
```


![변경전](https://user-images.githubusercontent.com/109357459/180462717-41b14741-0b66-444f-bd60-8f58c4a4a7b1.jpg)

![변경후](https://user-images.githubusercontent.com/109357459/180462720-bb6f2a3f-4579-4029-906b-28d0712a2564.jpg)

