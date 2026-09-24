# Minigame Template

Semion TD의 라이브러리와 Gradle 구성을 사용하는 서버 전용 Fabric 모드 템플릿이다. 게임 로직·운영 설정·리소스팩은 포함하지 않는다.

## 개발 환경

| 항목 | 버전 |
|---|---|
| Minecraft | 1.21.8 |
| Java | 21 |
| Gradle Wrapper | 9.2.1 |
| Fabric Loom | 1.15-SNAPSHOT |
| Fabric Loader | 0.19.2 |
| Fabric API | 0.136.1+1.21.8 |
| 매핑 | Mojang 공식 매핑 |

Java 21을 설치하고 `JAVA_HOME`을 해당 JDK로 설정한다. Gradle은 포함된 Wrapper로 실행한다.

```sh
./gradlew test runGameTest remapJar --console=plain --no-daemon
```

Windows에서는 `gradlew.bat`를 사용한다. 배포용 JAR은 `build/libs/minigame-template-1.0-SNAPSHOT+1.21.8.jar`다. `-sources.jar`는 소스 배포용이다.

`test`는 모드 메타데이터와 진입점을 확인하고, `runGameTest`는 전용 서버에서 모드와 주요 라이브러리의 로딩을 검사한다. GameTest는 Fabric API의 기본 빈 구조물을 사용하며 별도 맵이 필요 없다. 원본과 같은 GameTest 설정에 `eula = true`가 포함되어 있으므로 실행 전 Minecraft EULA를 확인한다.

개발 서버는 다음 명령으로 실행한다. 처음 실행하면 `run/eula.txt`에서 EULA 동의 여부를 직접 설정하고 다시 실행한다.

```sh
./gradlew runServer --console=plain
```

이 템플릿은 `environment: server`다. 클라이언트 화면이나 바닐라 클라이언트 접속 검증은 포함하지 않는다.

## 라이브러리

2026-09-24의 `steve-td` / `s3-release` 구성을 기준으로 직접 의존성의 버전·저장소·JAR 포함 방식을 유지했다. 기준 커밋은 `d35a4b3502e086a4d62b9df14da5ff511f23233a`다. 요청에 따라 `danta-shader`는 `kim.biryeong:minigame-lib:1.0.2+1.21.8`로 교체했다. Loom SNAPSHOT과 전이 의존성까지 영구 고정한 구성은 아니다.

- Polymer: core, virtual-entity, resource-pack, resource-pack-extras, autohost, blocks
- 화면·텍스트: SGUI, DialogUtils, Sidebar API, Server Translations, Adventure, MiniMessage, Placeholder API
- 월드·이벤트·모델: Fantasy, Map Templates, Stimuli, Avatar Renderer, Map Canvas API, Blockbench Import Library, Minigame Lib, GCB-server, APEL
- 기타: Player Data API, FactoryTools, Resourceful Lib, YACL, Hypersistence TSID, SQLite JDBC
- 개발·검증: Carpet, JUnit 5

전체 좌표와 버전은 `build.gradle`, `gradle.properties`에서 확인한다. `include(...)`로 선언한 의존성은 원본처럼 JAR 안에 넣으며, `modImplementation`만 선언한 모드는 배포 서버에도 설치해야 한다. Gradle 개발 실행에서는 의존성을 자동으로 가져온다. Carpet은 개발 실행 전용이며 배포 JAR에는 넣지 않는다.

Friends & Foes, Flowery Mooblooms와 두 Polymer 패치는 제외했다. 원본의 게임 코드·유료 에셋·DB·인증 정보·`run/mods` JAR도 복사하지 않았다.

## 새 프로젝트로 바꾸기

1. GitHub의 **Use this template**으로 저장소를 만든다.
2. `gradle.properties`의 `archives_base_name`, `maven_group`, `mod_version`과 `settings.gradle`의 프로젝트 이름을 바꾼다. `archives_base_name`은 모드 ID로도 사용하므로 소문자·숫자·밑줄·하이픈을 사용한다.
3. `MinigameTemplate.java`의 클래스·패키지·`MOD_ID`를 바꾸고 `src/main/resources/fabric.mod.json`의 이름·설명·진입점·저장소 주소를 맞춘다.
4. 테스트 패키지와 `src/gametest/resources/fabric.mod.json`의 ID·의존 모드 ID·진입점도 변경한다. 테스트 모드 ID는 새 모드 ID 뒤에 `-gametest`를 붙인다.
5. 위 검증 명령을 실행한다. 이후 게임 코드를 추가한다.

원본의 `All-Rights-Reserved` 정책을 유지했다. 공개 재사용을 허용하려면 `LICENSE`와 `fabric.mod.json`의 라이선스를 함께 변경한다. 외부 라이브러리는 각각의 라이선스를 따른다.

## Git과 배포

Gradle Wrapper는 추적하고 빌드 산출물·개발 월드·로그·환경 변수 파일·키 저장소는 제외한다. Maven 출판 설정은 준비되어 있지만 출판 대상과 자격 증명은 넣지 않았다. `build`나 `remapJar`는 운영 서버에 배포하지 않는다.
