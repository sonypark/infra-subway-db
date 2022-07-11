<p align="center">
    <img width="200px;" src="https://raw.githubusercontent.com/woowacourse/atdd-subway-admin-frontend/master/images/main_logo.png"/>
</p>
<p align="center">
  <img alt="npm" src="https://img.shields.io/badge/npm-%3E%3D%205.5.0-blue">
  <img alt="node" src="https://img.shields.io/badge/node-%3E%3D%209.3.0-blue">
  <a href="https://edu.nextstep.camp/c/R89PYi5H" alt="nextstep atdd">
    <img alt="Website" src="https://img.shields.io/website?url=https%3A%2F%2Fedu.nextstep.camp%2Fc%2FR89PYi5H">
  </a>
  <img alt="GitHub" src="https://img.shields.io/github/license/next-step/atdd-subway-service">
</p>

<br>

# 인프라공방 샘플 서비스 - 지하철 노선도

<br>

## 🚀 Getting Started

### Install
#### npm 설치
```
cd frontend
npm install
```
> `frontend` 디렉토리에서 수행해야 합니다.

### Usage
#### webpack server 구동
```
npm run dev
```
#### application 구동
```
./gradlew clean build
```
<br>

## 미션

* 미션 진행 후에 아래 질문의 답을 작성하여 PR을 보내주세요.


### 1단계 - 쿼리 최적화

1. 인덱스 설정을 추가하지 않고 아래 요구사항에 대해 1s 이하(M1의 경우 2s)로 반환하도록 쿼리를 작성하세요.

- 활동중인(Active) 부서의 현재 부서관리자 중 연봉 상위 5위안에 드는 사람들이 최근에 각 지역별로 언제 퇴실했는지 조회해보세요. (사원번호, 이름, 연봉, 직급명, 지역, 입출입구분, 입출입시간)

```sql
USE tuning;

SELECT top_5_manager.id as "사원번호", e.last_name as "이름", top_5_manager.annual_income as "연봉", p.position_name as "직급명", r.time as "입출입시간",r.region as "지역", r.record_symbol as "입출입구분" 
FROM
(SELECT id, start_date, annual_income 
FROM salary s
WHERE id IN 
(SELECT m.employee_id
FROM department d
JOIN manager m ON d.id = m.department_id and UPPER(d.note) = "ACTIVE"
WHERE m.start_date < now() and m.end_date > now()
) and s.end_date > now()
ORDER BY annual_income desc
LIMIT 5) top_5_manager
JOIN employee e on e.id = top_5_manager.id
JOIN position p ON p.id = top_5_manager.id and p.end_date > now()
JOIN record r ON r.employee_id = top_5_manager.id and r.record_symbol = "O";
```

```sql
14 row(s) returned	0.172 sec / 0.000011 sec
```

<img width="538" alt="result" src="https://user-images.githubusercontent.com/34808501/178293317-6b3696c9-e217-49c7-9d08-54030d6d3dd6.png">

![explain](https://user-images.githubusercontent.com/34808501/178293084-ba908499-f14e-408a-b88a-5f1aa8d88b43.png)
---

### 2단계 - 인덱스 설계

1. 인덱스 적용해보기 실습을 진행해본 과정을 공유해주세요

---

### 추가 미션

1. 페이징 쿼리를 적용한 API endpoint를 알려주세요
