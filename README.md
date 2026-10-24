# ESCALATION ROOM — GitHub Pages + Supabase

이 버전은 Render나 Node.js 서버 없이 **GitHub Pages(화면)**와 **Supabase Edge Function + PostgreSQL(데이터)**로 구성됩니다.

## 설정 순서
1. Supabase 프로젝트의 **SQL Editor**에서 `supabase/schema.sql` 전체 실행
2. Supabase **Edge Functions > Secrets**에서 `ADMIN_PASSWORD` 설정 (원하는 강력한 교수자 암호)
3. **Edge Functions > Deploy a new function > Via Editor**에서 함수 이름 `escalation-api`, `supabase/edge-function.ts` 코드를 붙여넣고 배포. 이 함수에서는 **Verify JWT 비활성화**. 함수 자체에서 관리자/참가자 토큰 권한을 검사합니다.
4. Supabase의 Project URL에서 프로젝트 ID를 복사해 `config.js`의 `YOUR_PROJECT_REF`를 바꿈 (DB 비밀번호와 service_role 키는 브라우저에 넣지 않습니다!)
5. GitHub Pages 저장소 최상위에 `index.html`, `app.js`, `config.js`, `assets` 폴더를 올리기. **`supabase` 폴더는 웹사이트에 업로드하지 않아도 됨**
6. GitHub 저장소 > Settings > Pages > Deploy from a branch > main, /(root) > Save
7. 공개 주소에서 관리자 수업 생성 및 참가자 입장 테스트

기존 `server.js`, `package.json`은 이 버전에서 사용하지 않습니다.

## 설계 및 안전 주의사항
- Supabase 테이블은 RLS 활성화 상태이며 브라우저에서 직접 접근하지 않습니다. Edge Function만 service-role 자격증명으로 읽고 씁니다.
- 반드시 Supabase Edge Function의 **ADMIN_PASSWORD**를 별도 설정하세요. 데이터베이스 암호와 별개입니다.
- Supabase의 데이터베이스 연결 문자열, database password, secret/service_role key는 GitHub에 절대 올리지 마세요.
- 기본 화면은 3초마다 동기화(polling)합니다. Socket.IO의 지속 연결 대신 브라우저가 서버리스 API를 주기적으로 조회합니다.
- 이 프로젝트는 교육용 시제품이며 실제 수업 전 반드시 여러 브라우저에서 테스트하세요. 짧은 기간에 다수 참가자가 동시에 투표할 경우 부하/요금 제한이 있을 수 있습니다.
- 비밀번호를 가진 교수자가 수업을 생성합니다. 학생은 수업 코드 및 본인 기기에 보존된 참가자 토큰으로 본인 응답을 수정합니다.
- GitHub Pages에서 공개 주소가 생성되기 전에는 웹페이지를 파일로 더블클릭해 열어도 CORS/보안 설정에 따라 작동하지 않을 수 있습니다.
