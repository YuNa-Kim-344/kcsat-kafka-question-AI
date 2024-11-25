# 수능 영어 AI 문제 생성 Kafka 서버

## 기술 이용
1. confluent_kafka 라이브러리를 사용하여 Kafka 메시지 스트리밍.
2. HuggingFace 라이브러리를 사용하여 로컬 AI 모델 파일 로드.
3. accelerate 라이브러리를 사용하여 멀티 GPU 할당.

## 핵심 구현 기능
1. Kafka 이벤트 스트리밍. 1초에 1번씩 Consume하여 쌓인 요청을 확인한다.
2. Fine-Tuned LLaMA-3 AI 문제 제작 요청
   - 항상 동일한 Output Format이 아니므로, 몇 가지 필터를 추가하였다. 필터를 통과하지 못하는 경우, 최대 7번까지 생성 시도를 반복한다.
     1. 문자 필터링 (러시아어, 한자, 일본어, 태국어, 베트남어, 아랍어, 특수 문자)
     2. 유형 개별 필터링
        1. 밑줄 유형 토큰 검증
        2. 빈칸 유형 토큰 검증
        3. 빈칸 A,B 유형 (A),(B) 토큰 검증
        4. 문법 유형 (1),(2),(3),(4),(5) 토큰 검증
        5. 요약문 A,B 유형 토큰 검증
        6. 순서 재배열 유형 (A),(B),(C) 토큰 검증
     3. 포맷 검증 필터링(제목, 본문, 보기가 잘 나왔는지. 보기는 5개인지.)