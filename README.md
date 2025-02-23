# 프론트엔드 배포 파이프라인

## 'Deploy Next.js to S3 and invalidate CloudFront' 워크플로우

![제목 없음-2025-02-18-2106](https://github.com/user-attachments/assets/6f95f4dd-52dd-4373-960a-0939e3d0be06)

### 워크플로우 과정

1. GitHub에서 제공하는 최신 버전의 Ubuntu 환경에서 실행
2. Checkout 액션을 사용해 코드 내려받기
3. `npm ci` 명령어로 프로젝트 의존성 설치
4. `npm run build` 명령어로 Next.js 프로젝트 빌드
5. AWS 자격 증명 구성
6. 빌드된 파일을 S3 버킷에 동기화
7. CloudFront 캐시 무효화

### 워크플로우가 실행되는 경우

- main 브랜치에 push가 발생할 때
- Actions 탭에서 수동으로 실행할 때

</br>

## 주요 링크

- S3 버킷 웹사이트 엔드포인트: http://hanghae-bucket-2dowon.s3-website.ap-northeast-2.amazonaws.com/
- CloudFront 배포 도메인 이름: https://d2m38adm0fof6k.cloudfront.net/

</br>

## 주요 개념

### GitHub Actions과 CI/CD 도구

**CI/CD 도구**

- CI/CD는 애플리케이션 배포 과정을 자동화하여 더 짧은 주기로 고객에게 서비스를 제공하는 방식
- CI (Continuous Integration, 지속적 통합)
  - 코드 변경 사항을 주기적으로 병합하고 자동으로 빌드 및 테스트하는 과정
- CD (Continuous Delivery / Continuous Deployment)
  - Continuous Delivery (지속적 전달): 코드 변경 사항이 자동으로 테스트 및 준비되어 운영 환경 배포 전 단계까지 자동화하는 방식입니다.
  - Continuous Deployment (지속적 배포): 코드 변경 사항이 자동으로 테스트를 거쳐 운영 환경까지 자동 배포되는 방식입니다.
- 대표적인 CI/CD 도구 : GitHub Actions, Jenkins, GitLab CI 등

**GitHub Actions**

- GitHub Actions는 GitHub에서 제공하는 CI/CD(Continuous Integration / Continuous Deployment) 자동화 도구
- 워크플로우(Workflow) 를 정의하여 코드 빌드, 테스트, 배포 등을 자동으로 실행할 수 있도록 도와준다

</br>

### S3와 스토리지

**S3**

- Amazon Simple Storage Service(S3)는 AWS에서 제공하는 객체 스토리지 서비스
- 대용량 데이터를 안정적으로 저장 가능
- 이미지, 동영상, 백업 파일 등 다양한 유형의 데이터를 저장할 수 있으며, 높은 가용성과 확장성을 제공

**스토리지**

- 스토리지는 데이터를 저장하고 검색할 수 있는 시스템
- 파일 스토리지(File Storage), 블록 스토리지(Block Storage), 객체 스토리지(Object Storage) 등 다양한 유형 존재
- Amazon S3는 객체 스토리지(Object Storage) 방식의 스토리지 서비스

</br>

### CloudFront와 CDN

**CloudFront**

- CloudFront는 AWS에서 제공하는 콘텐츠 전송 네트워크(CDN) 서비스
- 전 세계에 분산된 엣지 로케이션(Edge Location) 을 통해 웹 콘텐츠(이미지, 동영상, HTML, API 등)를 사용자에게 더 빠르고 안정적으로 전달하는 역할

**CDN(Content Delivery Network)**

- CDN은 전 세계에 분산된 서버 네트워크를 활용하여 웹 콘텐츠의 로딩 속도를 향상시키고, 서버 부하를 줄이는 역할
- CDN을 사용하면 웹사이트의 로딩 속도가 빨라지고, 서버의 부하 감소 => 전반적인 사용자 경험 향상

</br>

### 캐시 무효화(Cache Invalidation)

- 캐시 무효화는 캐시된 콘텐츠가 변경되었을 때 캐시를 갱신하는 과정
- CDN이나 브라우저 캐시를 사용하면 기존 콘텐츠가 저장되지만, 파일이 변경되었을 때 캐시가 오래된 상태로 유지 가능
- 캐시 무효화를 통해 사용자가 항상 최신 콘텐츠에 접근할 수 있도록 보장

</br>

### Repository secret과 환경변수

**Repository Secret**

- Repository Secret은 GitHub에서 제공하는 비밀 변수 저장소
- API 키, 데이터베이스 비밀번호 등의 민감한 정보를 안전하게 저장하고 사용 가능
- GitHub Actions에서 `${{ secrets.SECRET_NAME }}` 형식으로 접근 가능

**환경 변수 (Environment Variables)**

- 환경 변수는 애플리케이션이 실행되는 환경에서 설정 값을 저장하고 관리하는 변수
- `NODE_ENV=production`과 같이 애플리케이션의 실행 모드를 설정하는 데 사용
- GitHub Actions에서는 env 키워드를 사용하여 설정

</br>
</br>

# CDN과 성능 최적화

Cloudfront CDN 도입한 결과 성능 최적화가 가능합니다.
- 전체 페이지 로드 시간 3.46배 빠름
- DOM 콘텐츠 로드 시간 2.43배 빠름
- LightHouse 점수 향상


### LightHouse

- Cloudfront CDN 도입 후 'Best Practices(권장 사항)' 점수가 79점에서 100점으로 약 26.58% 향상

|               S3              |          Cloudfront CDN 도입 후           |
| :----------------------------------: | :----------------------------------: |
| ![image](https://github.com/user-attachments/assets/f81353eb-7c00-447e-9f21-49d0f5d015e7)| ![image](https://github.com/user-attachments/assets/2bf23fed-d7eb-4597-a173-8c5edab1db55) |



### Network

Cloudfront CDN 도입 후 아래처럼 **페이지 로드 속도가 향상**되었음을 확인 가능
- Transferred Data: 503kB에서 4.7kB로 99.07% 감소 (효율적인 캐싱 및 압축 효과)
- Finish Time: 165ms에서 48ms로 70.91% 감소
- DOMContentLoaded: 68ms에서 28ms로 58.82% 감소
- Load Time: 135ms에서 39ms로 71.11% 감소

|               S3              |          Cloudfront CDN 도입 후           |
| :----------------------------------: | :----------------------------------: |
| ![image](https://github.com/user-attachments/assets/9529d538-c1b8-4813-b72c-8ca2bf18c748) | ![image](https://github.com/user-attachments/assets/6a4a5a78-1b69-4b3f-a29d-bbcb45ba20c3) |


