[MDN web docs Django web framework(Python) 튜토리얼](https://developer.mozilla.org/ko/docs/Learn/Server-side/Django/Tutorial_local_library_website) 정리본

# LocalLibrary 웹사이트

이용자들이 대여 가능한 책을 찾아보고, 사용자 계정을 관리할 수 있는 **작은** 지역 도서관을 위한 온라인 도서목록을 제공하는 웹사이트

책, 책의 판본, 저자 및 다른 key 정보를 저장



1. Django 도구 사용: 웹 애플리케이션의 골격 만들기
2. 모델 생성: 애플리케이션 데이터의 틀 만들기
3. Django 관리자(admin) 사이트 사용: 데이터 입력
4. 뷰(view): 여러가지 요청에 따른 특정 데이터 가져오기
5. 템플릿 생성: 브라우저상에서 데이터를 볼 수 있도록 HTML로 렌더링
6. 매퍼(mappers) 생성: 여러가지 URL 패턴과 특정 뷰를 연결
7. 유저 인증(authorisation): 사이트의 동작과 접속을 통제
8. 세션 추가
9. 폼으로 작업
10. 앱을 테스트할 코드 작성
11. Django의 보안도구 사용
12. 운영환경에 배포



# 웹사이트의 뼈대 만들기

## 개요

1. `django-admin`
   - 프로젝트 폴더, basic file templates, 그리고 manage.py(프로젝트 관리 스크립트) 생성
2. `manage.py`
   - 하나 또는 그 이상의 애플리케이션 생성
   - 하나의 웹사이트는 하나 또는 그 이상의 section으로 구성
3. 새 애플리케이션 등록(register)하여 프로젝트에 포함
4. 각 애플리케이션에 대해 url/mapper 연결(hook up)



```shell
# manage.py 폴더 안에서 실행
# 새로운 폴더를 생성하고 폴더를 애플리케이션의 파일들로 채움
python3 manage.py startapp catalog
```



### 폴더 구조

locallibrary/

- 웹사이트 폴더

- `manage.py`
  - 프로젝트에 Django 도구를 실행하는 스크립트
  - django-admin을 사용하여 생성
  - 애플리케이션 생성, 데이터베이스와 작업, 개발 웹서버 시작을 위해 사용
- locallibrary/
  - 웹사이트/프로젝트 폴더
  - django-admin을 사용하여 생성
- catalog/
  - 응용 프로그램 폴더
  - manage.py를 사용하여 생성



## 프로젝트 생성

명령 프롬프트/터미널로 virtual environment 안에 있는지 확인

Django 앱을 저장할 곳에 웹 사이트 폴더 생성

```shell
mkdir locallibrary
cd locallibrary
```

새 프로젝트 생성

```shell
# 폴더/파일 구조 생성
django-admin startproject locallibrary
cd locallibrary
```



```shell
# manage.py 폴더 안에서 실행
# 새로운 폴더를 생성하고 폴더를 애플리케이션의 파일들로 채움
python3 manage.py startapp catalog
```



### 폴더 구조

locallibrary/

- `manage.py`
- locallibrary/
  - `__init__.py`
    - 빈 파일
    - Python에게 이 디렉토리를 하나의 Python Package로 다루도록 지시
  - `settings.py`
    - 웹사이트의 모든 설정을 포함
    - 생성한 모든 애플리케이션이 등록되는 곳
    - static files 위치, database 세부 설정, 등
  - `urls.py`
    - 사이트의 url과 view의 연결을 지정
    - 모든 url 매핑 코드가 포함될 수 있지만, 특정한 애플리케이션에 매핑의 일부를 할당해주는 것이 일반적
  - `wsgi.py`
    - Django 애플리케이션의 웹서버와 연결 및 소통하는 것을 도움
    - 이것을 표준 형식(boilerplate)으로 다루어도 무방



## catalog application 생성

locallibrary 프로젝트 안에 생성될 catalog 애플리케이션 생성

```shell
# manage.py 폴더 안에서 실행
# 새로운 폴더를 생성하고 폴더를 애플리케이션의 파일들로 채움
python3 manage.py startapp catalog
```



### 폴더 구조

locallibrary/

- `manage.py`
- locallibrary/
- catalog/
  - `admin.py`
    - 관리자 사이트 설정
  - `apps.py`
    - 애플리케이션 등록(registration)
  - `models.py`
    - model들 저장
  - `tests.py`
    - test들 저장
  - `views.py`
    - view들 저장
  - `__init__.py`
    - Django/Python이 폴더를 Python Package로 인식하게 할 빈 파일
    - 프로젝트의 다른 부분에서 객체(object)를 사용할 수 있게 함
  - migrations/
    - migration들을 저장할 폴더
    - 모델을 수정할 때마다 자동으로 database를 업데이트할 수 있게 함



## catalog application 등록

도구가 실행될 때 프로젝트에 포함시키기 위해 프로젝트에 등록

프로젝트 설정 안 `INSTALLED_APPS` 리스트에 추가

```python
# locallibrary/locallibrary/settings.py
INSTALLED_APPS = [
    ...
    'catalog.apps.CatalogConfig', 
]
```

- 애플리케이션 구성 객체 `CatalogConfig`를 지정
- 애플리케이션을 생성할 때 /locallibrary/catalog/apps.py 안에 생성됨
- 많은 `INSTALLED_APPS`, `MIDDLEWARE`들을 통해 Django administration을 지원할 수 있음
  - Django 관리 사이트가 사용하는 많은 기능들(세션, 인증, 등)을 지원



## Database 설정

프로젝트에 사용할 database를 지정

가능한 개발과 결과물에 동일한 database를 사용해 사소한 동작 차이를 방지해야 함

```python
# locallibrary/locallibrary/settings.py
# SQLite database 사용
# 데모 database에서 많은 동시 접속을 예상하지 않고, 설정에 추가 작업이 필요없음
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': 'os.path.join(BASE_DIR, 'db.sqlite3')',
    }
}
```



## 프로젝트의 다른 설정

`settings.py`는 또한 많은 설정을 조정하는 데 사용

- `TIME_ZONE`

  - 표준화된 List of tz database time zones와 일치되는 문자열을 사용해야 함

  ```python
  # locallibrary/locallibrary/settings.py
  TIME_ZONE = 'Europe/London'
  ```

- `SECRET_KEY`
  - Django의 웹사이트 보안 전략의 일부로 사용되는 비밀 키
  - 만약 개발 과정에서 이 코드를 보호하지 않는다면, 제품화(production) 과정에서 다른 코드를 사용해야 할 것
    - 환경 변수나 파일에서 읽어오는 코드

- `DEBUG`

  - 에러가 발생했을 때 HTTP 상태코드 응답 대신 디버깅 로그가 표시되게 함

  - 디버깅 정보는 공격자에게 유용하기 때문에 제품화된(production) 환경에서는 `False`로 설정해야 함



## URL 매퍼 연결

웹사이트는 프로젝트 폴더 안의 URL mapper file(`urls.py`)과 같이 생성

모든 URL 매핑들을 관리할 수 있지만, 연관된 애플리케이션에 따라 매핑을 다르게 하는 것이 일반적



URL 매핑

- `path()` 함수 list 변수인 `urlpatterns`를 통해 관리
- 맨 처음에 `admin.site.urls` 모듈에 admin/ 패턴을 갖고 있는 모든 URL을 매핑하는 단일 함수를 정의
- `admin.site.urls` 모듈은 관리자 애플리케이션의 고유한 URL 매핑 정의를 가짐
- 각각의 `path()` 함수는 패턴이 일치할 때 표시될 지정된 뷰에 URL 패턴을 연결하거나, 다른 URL 패턴 테스트 코드 목록에 연결
- `path()` 속의 경로는 일치시킬 URL 패턴을 정의하는 문자열



```python
# locallibrary/locallibrary/urls.py
from django.contrib import admin
from django.urls import path
from django.conf.urls import include

# 기본 URL을 애플리케이션으로 redirection하기 위해 URL 맵 추가
from django.views.generic import RedirectView

# static()을 사용하여 개발 중에 정적 파일을 제공하기 위한 URL 매핑 추가
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    
    # 요청(request)을 모듈 'catalog.urls'에 'catalog/' 패턴과 함께 전달하는 path()를 포함
    path('catalog/', include('catalog.urls')),
    
    # 사이트의 루트 URL로 redirect하기 위해 특별한 뷰 함수 RedirectView 사용
    # path()에서 지정된 URL 패턴이 일치할 때, 첫번째 인자를 '/catalog/'로 redirect할 새로운 상대 URL로 간주
    path('', RedirectView.as_view(url='/catalog/', permanent=True)),
    
    # 개발 중에 (CSS, JavaScript, 이미지 등과 같은) 정적 파일들을 제공할 수 있게 함
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```



catalog 폴더 안에 `urls.py` 파일 생성

```python
# locallibrary/catalog/urls.py
from django.urls import path
from catalog import views

urlpatterns = []
```



## Website framework 테스트

프로젝트가 오류 없이 돌아가는지 한 번 실행해보기

1. database 마이그레이션(migration) 실행

   - database에 애플리케이션에 속한 모든 model들을 포함하도록 업데이트
   - 몇몇 빌드 경고의 원인을 제거
   - Django는 ORM을 사용하여 Django 코드 안에 있는 모델 객체(정의)를 기본 database에서 사용하는 데이터 구조(관계형 DB)에 매핑
     - ORM: Object-Relational-Mapper, 객체-관계-매퍼
   - database 안의 기본 데이터 구조가 모델과 일치하도록 자동적으로 migrate하는 스크립트를 (/locallibrary/catalog/migrations/ 안에) 생성
     - 모델의 정의를 바꿀 때마다 변화를 추적함
   - 웹사이트를 생성할 때 Django는 사이트의 관리자 섹션에서 사용할 여러 모델들을 자동으로 추가

   ```shell
   # database 안의 모델들을 위한 테이블들을 정의
   # 모델이 변경될 때마다 실행
   pyhton3 manage.py makemigrations
   python3 manage.py migrate
   ```

   - `makemigrations`
     - 프로젝트에 설치된 모든 애플리케이션에 대한 migration들을 생성 (적용하진 않음)
     - migration들이 적용되기 전에 코드를 점검할 수 있음
   - `migrate`
     - migration들을 실제로 database에 적용
     - Django는 현재 database에 어떤 것들이 추가됐는지 추적

2. 웹사이트 실행

   - 개발용 웹서버를 사용하여 로컬 웹 브라우저에서 볼 수 있음
   - 개발 중에 편하고 빠른 테스트를 위해 Django 웹사이틀르 실행할 수 있는 아주 쉬운 방법

   ```shell
   # 개발 웹서버 실행
   python3 manage.py runserver
   ```

   - 서버가 실행된다면 로컬 웹 브라우저에서 사이트를 볼 수 있음
   - 아직 `catalog.urls` 모듈 안에 정의된 page/url들이 없음
     - 사이트 에러 페이지가 뜸
     - 자동화된 디버그 기록(logging)을 보여줌
       - Django의 중요한 기능
       - 페이지를 찾을 수 없거나, 코드에서 에러가 발생했을 어떤 때라도 유용한 정보가 표시됨
       - 웹에 라이브로 사이트를 올려놓으면 디버그 기록이 꺼져 있을 것임



# 모델 사용

## 개요

모델

- Python 객체

- Django 웹 애플리케이션들은 모델을 통해 데이터에 접속하고 관리
- 저장된 데이터의 구조를 정의
  - 필드 타입, 데이터의 최대 크기, 기본값, 선택-리스트 옵션, 문서를 위한 도움 텍스트, 폼을 위한 라벨 텍스트, 등등
- database와 소통하는 것은 Django가 해줌



## LocalLibrary models 디자인

어떤 데이터를 저장할 것인가?

- 책에 관한 정보들: 제목, 요약, 저자, 작성된 언어, 분류, ISBN
- 여러 개의 사본을 사용할 수 있어야 함: 고유 ID, 가용성 상태, 등
- 저자에 관한 정보들
- 정보 정렬: 책 제목, 저자, 언어, 분류

다른 객체들에 대한 관계를 어떻게 지정할 것인가?

- 관계 설정: 일대일(`OneToOneField`), 일대다(`ForeignKey`), 다대다(`ManyToManyField`) 관계



모델 디자인

- 각각의 객체마다 분리된 모델을 가지는 것이 타당
- 객체들: **책, 책 인스턴스, 저자**
  - 책(Book): 책의 일반적인 세부 사항등
  - 책 인스턴스(BookInstance): 시스템에서 사용 가능한 책의 특정 (물리적) 복사본 상태
    - `BookInstance:statsus`에 대한 모델을 생성하지 않고 (변하지 않는) 값들을 하드코딩
  - 저자(Author)
- 모델을 사용해 선택-리스트 옵션을 나타내도록 함
  - 모든 옵션을 미리 알 수 없거나 옵션이 변할 수 있을 때 추천
  - 모델들: **책 장르, 언어**
    - 장르(Genre): 값들이 관리자 인터페이스에서 생성 및 선택 가능하도록 함
    - 언어(Language)



## 모델 입문

### 모델 정의

모델들은 보통 애플리케이션의 `models.py` 파일에서 정의

`django.db.models.Model`의 서브클래스로 구현

필드, 메소드, 메타데이터를 포함할 수 있음



```python
from django.db import models

# MyModelName 모델
# Model 클래스에서 파생된 모델을 정의하는 클래스
class MyModelName(models.Model):
    # 필드
    my_field_name = models.CharField(max_length=20, help_text='Enter field documentation')
    
    # 메타데이터
    class Meta:
        ordering = ['-my_field_name']
        
    # 메소드
    # MyModelName의 특정 인스턴스에 액세스하기 위한 URL을 반환
    def get_absolute_url(self):
        return reverse('model-detail-view', args=[str(self.id)])
    
    # MyModelName 객체를 나타내는 문자열
    def __str__(self):
        return self.field_name
```



#### 필드(Fields)

database 목록(table)에 저장하길 원하는 데이터 열(column)을 나타냄

각각의 database 레코드(행, row)는 각 필드 값들 중 하나로 구성



`my_field_name = models.CharField(max_length=20, help_text='Enter field documentation')`

- `my_field_name` 필드를 갖고 있고, `models.CharField` 타입
  - 영숫자 문자열을 포함하는 필드
- 인수
  - `max_length=20`: 값의 최대 길이는 20자
  - `help_text='Enter field documentation'`: HTML 양식(form)에서 사용자들에게 입력될 때 어떤 값을 입력해야 하는지 알려주기 위해 보여주는 텍스트 라벨
- 라벨
  - 필드 변수 이름의 첫자를 대문자로 바꾸고 밑줄을 공백으로 바꿔 기본 라벨을 추정
    - 'My field name'을 기본 라벨로 갖고 있음
  - 인수로 지정된 라벨(`verbose_name`)을 가질 수도 있음



##### 일반적인(common) 필드 인수

(대부분의) 서로 다른 필드 타입들을 선언할 때 사용 가능

- `help_text`
- `verbose_name`
- `default`
- `null`
- `blank`
- `choices`
- `primary_key`



##### 일반적인(common) 필드 타입

#### 메타데이터

#### 메소드(Methods)

### 모델 관리(management)

#### 레코드 생성 및 수정

#### 레코드 검색



## LocalLibrary 모델 정의

`models.py` 파일에서 도서관을 위한 모델을 정의

```python
# /locallibrary/catalog/models.py
# 표준 코드(boilerplate)
# 모델이 상속받을 모델 기본 클래스 models.Model을 포함하는 models 모듈 가져옴(import)
from django.db import models
```



### Genre 모델

책 카테고리에 관한 정보를 저장하는 데 사용되는 모델

장르를 자유 텍스트나 선택 목록으로 만들지 않고 모델을 이용해 생성

- 값들이 하드코딩되기보다 database를 통해 관리되도록 함

```python
# 책 장르를 나타내는 모델
class Genre(models.Model):
    # 장르의 이름을 나타내는 CharField 필드
    # 200자로 제한, 몇 가지 help_text를 가짐
    name = models.CharField(max_length=200, help_text='Enter a book genre')
    
    # __str__() 메소드 선언
    # 특정 레코드에 의해 정의된 장르의 이름을 반환
    # verbose name이 정의되지 않았기 때문에, 필드는 form에서 Name으로 호출(call)
    # Model 객체를 나타내는 문자열
    def __str__(self):
        return self.name
```



### Book 모델

일반적으로 사용 가능한 책에 대한 모든 정보들을 보여주는 모델

대여 가능한 특정 (물리적) "인스턴스"나 "복사본"은 보여주지 않음

```python
# URL 패턴을 반대로 하여 URL을 생성하는 데 사용
from django.urls import reverse

# 책을 나타내는 모델 (책의 특정 사본은 아님)
class Book(models.Model):
    title = models.CharField(max_length=200)

    # 각각의 책은 하나의 저자만 가지고, 저자는 여러 개의 책들을 가짐
    # Author 클래스가 파일에서 아직 선언되지 않았기 때문에, 객체가 아닌 문자열로 작성
    author = models.ForeignKey('Author', on_delete=models.SET_NULL, null=True)
    
    # 텍스트가 상당히 길 것임
    summary = models.TextField(max_length=1000, help_text='Enter a brief description of the book')
    
    # 이름을 지정하지 않은 첫번째 매개변수를 사용해 라벨을 "ISBN"으로 지정
    # 만약 지정하지 않았다면, 기본 라벨을 "Isbn"
    isbn = models.CharField('ISBN', max_length=13, help_text='13 Character <a href="https://www.isbn-international.org/">ISBN number</a>')
    
    # 책이 여러 개의 장르를 가지고, 장르도 여러 개의 책을 가질 수 있음
    # Genre 클래스가 이미 정의되어 있기 때문에, 객체를 지정할 수 있음
    genre = models.ManyToManyField(Genre, help_text='Select a genre for this book')
    
    language= models.ForeignKey('Language', on_delete=models.SET_NULL, null=True)
    
    # Book 레코드를 나타내는 책의 title 필드를 사용해 정의
    def __str__(self):
        return self.title
    
    # 이 모델의 세부 레코드(상세 정보)에 접근하기 위한 URL을 반환
    # 'book-detail' 이름의 URL 매핑을 정의하고, 관련 뷰와 템플릿을 정의해야 함
    def get_absolute_url(self):
        return reverse('book-detail', args=[str(self.id)])
```

- Genre와 Author 모델 클래스

  - `ManyToManyField`와 `ForeignKey` 필드 타입 안에서 관련

  -  모델 클래스가 관련된 모델의 이름을 포함하는 문자열을 사용해 이름 없는 첫번째 매개변수로 선언
  - 연관된 클래스가 참조되기 전에 파일 안에서 아직 정의되지 않았다면 모델의 이름을 문자열로 사용

- author 필드

  - `null=True`
    - 어떤 저자도 선택되지 않으면 database에 `Null` 값을 저장
  - `on_delete=models.SET_NULL`
    - 관련된 author 레코드가 삭제됐을 때 author 값을 `Null`로 설정



### BookInstance 모델

누군가 빌릴지도 모를 특정한 책의 본사본을 나타내는 모델

복사본 사용 가능 여부, 되돌려받을 수 있는 날짜, 출판사(imprint) 또는 버전 세부 사항, 도서관에 있는 책의 고유 id 정보를 포함

```python
고유한 book instance에 필요
import uuid

# (도서관에서 빌릴 수 있는) 책의 특정 사본을 나타내는 모델
class BookInstance(models.Model):
    # id 필드가 이 모델의 primary key로 설정되는 데 사용
    # 각 인스턴스에 전역적으로 고유한 값을 할당 (도서관에서 찾을 수 있는 모든 책마다)
    id = models.UUIDField(primary_key=True, default=uuid.uuid4, help_text='Unique ID for this particular book across whole library')
    
    # 연관된 Book을 식별하기 위한 필드
    # 각각의 책은 많은 복사본을 가지고, 복사본은 하나의 Book만 가짐
    book = models.ForeignKey('Book', on_delete=models.SET_NULL, null=True) 
    
    # 책의 출판사(특정 발간일)를 나타내기 위한 필드
    imprint = models.CharField(max_length=200)
    
    # 책이 빌려지거나 유지보수된 이후 사용할 수 있을 것으로 예상되는 날짜
    # 책을 사용할 수 있는 경우 필요(null이나 blank가 될 수 있음)
    due_back = models.DateField(null=True, blank=True)

    # key-value 쌍의 튜플을 포함하는 튜플
    # value는 사용자가 선택할 수 있는 표시값, key는 선택됐을 때 실제로 저장되는 값
    LOAN_STATUS = (
        ('m', 'Maintenance'),
        ('o', 'On loan'),
        ('a', 'Available'),
        ('r', 'Reserved'),
    )

    # 선택/선택 목록을 정의
    # LOAN_STATUS 튜플을 정의해 choices 인자에 전달
    # 책이 선반에 저장되기 전에는 사용할 수 없기 때문에 기본값 'm' 설정
    status = models.CharField(
        max_length=1,
        choices=LOAN_STATUS,
        blank=True,
        default='m',
        help_text='Book availability',
    )

    # 메타데이터 모델
    class Meta:
        # 레코드들이 쿼리에서 반환됐을 때 레코드들을 정렬
        ordering = ['due_back']

    # 고유 id와 연관된 Book의 제목을 조합해 BookInstance 객체를 나타냄
    def __str__(self):
        return f'{self.id} ({self.book.title})'
```



### Author 모델

저자의 이름, 성, 생일, (선택적으로) 사망일을 가진 모델

```python
# 저자를 나타내는 모델
class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    date_of_birth = models.DateField(null=True, blank=True)
    date_of_death = models.DateField('Died', null=True, blank=True)

    class Meta:
        ordering = ['last_name', 'first_name']
    
    # 특정 author instance에 접근하기 위한 URL을 반환
    # URL을 가져오기 위해 'author-detail' URL 매핑을 반대로 함
    def get_absolute_url(self):
        return reverse('author-detail', args=[str(self.id)])

    def __str__(self):
        return f'{self.last_name}, {self.first_name}'
```



### Language 모델

```python
# 언어를 나타내는 모델
class Language(models.Model):
    name = models.CharField(max_length=200,
                            help_text="Enter the book's natural language")

    def __str__(self):
        return self.name
```



## database migration 재실행

모든 모델이 생성된 후, database migration을 재실행하여 모델들을 database에 추가

```shell
python3 manage.py makemigrations
python3 manage.py migrate
```



# Django admin site

## 개요

## Models 등록

## Superuser 생성

## 관리자 계정에 로그인

## 추가 설정

### ModelAdmin 클래스 등록

### 목록 뷰들 설정

### 목록 필터 추가

### 세부 뷰 레이아웃 조직

#### 어떤 필드들이 보여지고 배치될지 제어

#### 세부 뷰 구역나누기

### 연관 레코드들의 인라인 편집



# Creating our home page

# Generic list and detail views

# Sessions framework

# User authentication and permissions

# Working with forms

# Testing a Django web application

# Deploying Django to production