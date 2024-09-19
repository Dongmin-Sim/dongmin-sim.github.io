---
title: 컬럼 카디널리티에 따른 인덱스 성능 비교
author: codongmin
date: 
categories: 
tags: 
preview:
---

  
주제 
- 컬럼의 카디널리티에 따른 인덱스의 성능 차이를 알아본다.
- 컬럼의 카디널리티를 고려하여 인덱싱하는 방법, 전략을 제시한다.

목차
- 개요
- 카디널리티란?
- 인덱스의 동작 방식
- 카디널리티에 따른 인덱스의 성능 비교
	- 카디널리티별 차이
- 카디널리티를 고려한 인덱싱 전략
    - 카디널리티가 높은 컬럼부터 인덱싱    
- 마무리
- ---

## 들어가며


## 기록 조회 

```sql
select doctorId, dischargeDate, count(id) as count  
from medical_record
where doctorId = 1 and medical_record.dischargeDate between '2013-01-01' and '2023-12-31'  
group by doctorId, dischargeDate  
order by count desc
```

의사별 퇴원 진료 기록 통계 , 
- 의사 ID 1을 가진, 퇴원 일자별 환자 수

데이터 분포
![[Pasted image 20240621200148.png]]
![[Pasted image 20240621201530.png]]

Index : doctorId, discharge, complex(doctorId, dischargeDate)

| 인덱스 무  | doctorId 인덱스 유 | discharge 인덱스 유 | 복합인덱스 |     |
| ------ | -------------- | --------------- | ----- | --- |
| 1055ms | 3617           | 4296           | 532   |     |
| 913    | 3246           | 3473            | 537   |     |
| 952    | 3224           | 3422            | 584   |     |
| 946    | 3215           | 3479            | 553   |     |
| 935    | 3356           | 3435            | 563   |     |
인덱스 무 - 실행 계획
```
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra  
1,SIMPLE,medical_record,,ALL,,,,,996158,11.11,Using where; Using temporary; Using filesort
```

인덱스 유 - 실행 계획
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ref,MEDICAL_RECORD__ix_doctorId,MEDICAL_RECORD__ix_doctorId,8,const,498079,11.11,Using where; Using temporary; Using filesort

```

discharge index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,index,MEDICAL_RECORD__ix_dischargeDate,MEDICAL_RECORD__ix_dischargeDate,6,,996158,5,Using where; Using temporary; Using filesort

```

complex index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,range,MEDICAL_RECORD__ix_complex,MEDICAL_RECORD__ix_complex,14,,498079,100,Using where; Using index; Using temporary; Using filesort

```

--- 
데이터 분포

![[Pasted image 20240621202527.png]]

![[Pasted image 20240621202541.png]]



| 인덱스 무 | doctorId 인덱스 유 | discharge 인덱스 유 | 복합인덱스 |
| ----- | -------------- | --------------- | ----- |
| 711   | 1737           | 4803            | 426   |
| 734   | 1718           | 4648            | 459   |
| 711   | 1736           | 4665            | 417   |
| 739   | 1720           | 4644            | 369   |
| 719   | 1808           | 4646            | 390   |
인덱스 무 - 실행 계획
```
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ALL,,,,,994015,11.11,Using where; Using temporary; Using filesort

```

doctorId 인덱스 유 - 실행 계획
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ref,MEDICAL_RECORD__ix_doctorId,MEDICAL_RECORD__ix_doctorId,8,const,497007,11.11,Using where; Using temporary; Using filesort
```

discharge index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,index,MEDICAL_RECORD__ix_dischargeDate,MEDICAL_RECORD__ix_dischargeDate,6,,994015,5,Using where; Using temporary; Using filesort

```

complex index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,range,MEDICAL_RECORD__ix_complex,MEDICAL_RECORD__ix_complex,14,,497007,100,Using where; Using index; Using temporary; Using filesort

```

---
데이터 분포

![[Pasted image 20240621204255.png]]

![[Pasted image 20240621202541.png]]



| 인덱스 무 | doctorId 인덱스 유 | discharge 인덱스 유 | 복합인덱스 |
| ----- | -------------- | --------------- | ----- |
|       |                |                 |       |
|       |                |                 |       |
|       |                |                 |       |
|       |                |                 |       |
|       |                |                 |       |
인덱스 무 - 실행 계획
```
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ALL,,,,,997088,2.78,Using where; Using temporary; Using filesort


```

doctorId 인덱스 유 - 실행 계획
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ref,MEDICAL_RECORD__ix_doctorId,MEDICAL_RECORD__ix_doctorId,8,const,432844,11.11,Using where; Using temporary; Using filesort

```

discharge index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,index,MEDICAL_RECORD__ix_dischargeDate,MEDICAL_RECORD__ix_dischargeDate,6,,997088,5,Using where; Using temporary; Using filesort

```

complex index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,range,MEDICAL_RECORD__ix_complex,MEDICAL_RECORD__ix_complex,14,,234172,100,Using where; Using index; Using temporary; Using filesort
```

---
![[Pasted image 20240621205805.png]]


인덱스 무 - 실행 계획
```
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ALL,,,,,997219,1.23,Using where; Using temporary; Using filesort


```

doctorId 인덱스 유 - 실행 계획
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ref,MEDICAL_RECORD__ix_doctorId,MEDICAL_RECORD__ix_doctorId,8,const,206690,11.11,Using where; Using temporary; Using filesort
```

discharge index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,index,MEDICAL_RECORD__ix_dischargeDate,MEDICAL_RECORD__ix_dischargeDate,6,,997219,5,Using where; Using temporary; Using filesort

```

complex index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,range,MEDICAL_RECORD__ix_complex,MEDICAL_RECORD__ix_complex,14,,112332,100,Using where; Using index; Using temporary; Using filesort

```

---

![[Pasted image 20240621210845.png]]


인덱스 무 - 실행 계획
```
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ALL,,,,,995262,0.62,Using where; Using temporary; Using filesort
```

doctorId 인덱스 유 - 실행 계획
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,ref,MEDICAL_RECORD__ix_doctorId,MEDICAL_RECORD__ix_doctorId,8,const,100346,11.11,Using where; Using temporary; Using filesort

```

discharge index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,index,MEDICAL_RECORD__ix_dischargeDate,MEDICAL_RECORD__ix_dischargeDate,6,,995262,5,Using where; Using temporary; Using filesort

```

complex index - explain
```csv
id,select_type,table,partitions,type,possible_keys,key,key_len,ref,rows,filtered,Extra
1,SIMPLE,medical_record,,range,MEDICAL_RECORD__ix_complex,MEDICAL_RECORD__ix_complex,14,,50396,100,Using where; Using index; Using temporary; Using filesort
```

## 마무리