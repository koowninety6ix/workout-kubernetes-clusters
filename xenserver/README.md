# XCP-ng 8.2.1 (20231130) 설치 
[다운로드 링크](https://xcp-ng.org/#easy-to-install)

## AMD CPU 환경에서 설치 시 발생하는 에러

설치 중 아래와 같은 에러가 발생할 수 있습니다:

```
panic on cpu 0: xen_bug: iommu_init.c:1425
```

이 에러는 **IOMMU** 기능과 관련이 있으며, 보통 바이오스(BIOS) 설정에서 **IOMMU 옵션**을 비활성화하면 해결됩니다.  
하지만 일부 메인보드에서는 제조사에서 해당 옵션을 숨겨놓아 직접 설정할 수 없는 경우가 있습니다.

---

## 해결 방법

### 1. 설치 메뉴에서 `iommu=off` 설정

1. 설치 초기 화면에서 설치 메뉴를 선택하지 말고, 키보드에서 **e 키**를 눌러 **편집 모드**로 진입합니다.
2. `multiboot2 /boot/xen.gz`로 시작하는 라인을 찾습니다.
3. **라인 제일 위에** `iommu=off` 옵션을 추가합니다.

    예시:
    ```
    multiboot2 /boot/xen.gz iommu=off ...
    ```
4. 편집을 완료한 후 **Ctrl + X**를 눌러 설치를 진행합니다.

---

### 2. 설치 후 grub 설정 수정

설치 및 부팅 시마다 매번 `iommu=off`를 적용해야 하므로, grub 설정 파일을 수정해 자동으로 적용되게 합니다.

```bash
vi /boot/grub/grub.cfg
```

`/boot/xen.gz`가 등장하는 줄을 찾아 **iommu=off** 옵션을 추가합니다.

예시:
```
multiboot2 /boot/xen.gz iommu=off ...
```

변경 후 저장하면 다음 부팅부터는 수동 입력 없이 자동 적용됩니다.

---

## XCP-ng Center 설치

- **버전**: XCP-ng Center 20.04.01.33
- 설치 방법은 일반 윈도우 프로그램 설치 방식과 동일합니다. (설치 파일 실행 → Next → 완료)

[공식 다운로드 링크](https://github.com/xcp-ng/xenadmin/releases)

---

## 정리

| 항목                     | 내용                          |
|---------------------------|-------------------------------|
| 발생 에러                | `panic on cpu 0: xen_bug: iommu_init.c:1425` |
| 임시 해결 방법           | 설치 메뉴 편집 후 `iommu=off` 추가 |
| 영구 해결 방법           | `/boot/grub/grub.cfg` 수정 |
| XCP-ng Center 버전        | 20.04.01.33 |
