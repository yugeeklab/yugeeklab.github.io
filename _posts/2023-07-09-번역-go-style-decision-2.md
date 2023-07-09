---
title: "[번역] Go Style Decision 2"
date: 2023-07-09T11:53:35.916Z
categories:
  - golang
tags:
  - golang
math: false
---
## **반복**

Go 소스 코드는 불필요한 반복을 피해야 합니다. 주로 보이는 실수는 불필요한 단어를 포함하거나 컨텍스트나 유형을 반복하는 것입니다. 코드 자체도 같거나 유사한 코드 세그먼트가 근접한 위치에서 여러 번 나타나면 불필요하게 반복적일 수 있습니다.

반복적인 이름은 다양한 형태로 나타날 수 있습니다.

### **Package vs. exported symbol name**

**exported symbol name**을 지정할 때 패키지 이름은 항상 패키지 외부에서 가시적이므로 두 곳 사이의 중복 정보를 줄이거나 제거해야 합니다. 패키지가 하나의 유형만 내보내고 해당 유형이 패키지 자체의 이름으로 지정된 경우, 생성자의 규범적인 이름은 필요한 경우 **`New`**입니다.

예시: 반복적인 이름 -> 더 나은 이름

* **`widget.NewWidget`** -> **`widget.New`**
* **`widget.NewWidgetWithName`** -> **`widget.NewWithName`**
* **`db.LoadFromDatabase`** -> **`db.Load`**
* **`goatteleportutil.CountGoatsTeleported`** -> **`gtutil.CountGoatsTeleported`** 또는 **`goatteleport.Count`**
* **`myteampb.MyTeamMethodRequest`** -> **`mtpb.MyTeamMethodRequest`** 또는 **`myteampb.MethodRequest`**

### **Variable name vs. type**

컴파일러는 변수의 유형을 항상 알고 있으며 대부분의 경우 독자가 변수의 유형을 사용 방법에 따라 명확히 알 수 있습니다. 변수의 값이 동일한 스코프에서 두 번 나타날 경우에만 변수의 유형을 명확히 하는 것이 필요합니다.

| 반복적인 이름                     | 더 나은 이름              |
| --------------------------- | -------------------- |
| var numUsers int            | var users int        |
| var nameString string       | var name string      |
| var primaryProject *Project | var primary *Project |

값이 여러 형태로 나타날 경우 추가적인 단어(예: **`raw`**와 **`parsed`**)로 명확히 할 수도 있고, 기본 표현으로 나타낼 수도 있습니다.

```
// Good:
limitStr := r.FormValue("limit")
limit, err := strconv.Atoi(limitStr)

// Good:
limitRaw := r.FormValue("limit")
limit, err := strconv.Atoi(limitRaw)
```

### **External context vs. local names**

주변 컨텍스트를 포함하는 이름은 종종 불필요한 잡음을 만들 수 있습니다. 패키지 이름, 메서드 이름, 유형 이름, 함수 이름, import 경로, 심지어 파일 이름까지 모두 주변 컨텍스트를 제공하여 내부의 모든 이름을 충분하게 만들어줍니다. (간결한 이름으로도 충분합니다.)

```
// Bad:
// In package "ads/targeting/revenue/reporting"
type AdsTargetingRevenueReport struct{}

func (p *Project) ProjectName() string

// Good:
// In package "ads/targeting/revenue/reporting"
type Report struct{}

func (p *Project) Name() string

// Bad:
// In package "sqldb"
type DBConnection struct{}

// Good:
// In package "sqldb"
type Connection struct{}

// Bad:
// In package "ads/targeting"
func Process(in *pb.FooProto) *Report {
    adsTargetingID := in.GetAdsTargetingID()
}

// Good:
// In package "ads/targeting"
func Process(in *pb.FooProto) *Report {
    id := in.GetAdsTargetingID()
}
```

반복은 일반적으로 심볼의 사용자의 문맥을 고려하여 평가해야 합니다. 예를 들어, 다음 코드는 어떤 상황에서는 괜찮을 수 있는 많은 이름을 가지고 있지만, 문맥에서는 중복된 것으로 판단되는 많은 이름을 가지고 있습니다.

```
// Bad:
func (db *DB) UserCount() (userCount int, err error) {
    var userCountInt64 int64
    if dbLoadError := db.LoadFromDatabase("count(distinct users)", &userCountInt64); dbLoadError != nil {
        return 0, fmt.Errorf("failed to load user count: %s", dbLoadError)
    }
    userCount = int(userCountInt64)
    return userCount, nil
}
```

대신, 문맥이나 사용법에서 명확한 이름에 대한 정보는 생략할 수 있습니다.

```
// Good:
func (db *DB) UserCount() (int, error) {
    var count int64
    if err := db.Load("count(distinct users)", &count); err != nil {
        return 0, fmt.Errorf("failed to load user count: %s", err)
    }
    return int(count), nil
}
```

## **Commentary (주석)**

주석에 대한 규칙(무엇을 주석으로 처리해야 하고 어떤 스타일을 사용해야 하며 실행 가능한 예제를 어떻게 제공해야 하는지 등)은 공개 API의 문서를 읽는 경험을 지원하기 위해 고안되었습니다. 자세한 내용은 **[Effective Go](http://golang.org/doc/effective_go.html#commentary)**를 참조하십시오.

문서 규칙에 대한 가이드라인은 **[best practices document](https://google.github.io/styleguide/go/best-practices#documentation-conventions)**의 "documentation conventions" 섹션에서 자세히 설명합니다.

**Best Practice**: 개발 중 및 코드 리뷰 중에 문서와 실행 가능한 예제가 유용하고 예상한 대로 표시되는지 확인하기 위해 **[doc preview](https://google.github.io/styleguide/go/best-practices#documentation-preview)**를 사용하세요.

**Tip**: Godoc은 특수한 서식을 거의 사용하지 않으며, 목록과 코드 조각은 일반적으로 줄 바꿈을 피하기 위해 들여쓰기되어야 합니다. 들여쓰기 외에는 일반적으로 꾸미기를 피해야 합니다.

### **Comment line length (주석 줄 길이)**

소스에서 주석이 좁은 화면에서도 읽을 수 있도록 보장하세요.

주석이 너무 길어질 경우, 여러 줄 주석으로 나누는 것이 좋습니다. 가능한 경우, 80컬럼 넓이의 터미널에서 잘 읽히는 주석을 목표로 하지만, 이는 강제적인 제한이 아닙니다. 예를 들어, 표준 라이브러리는 종종 문장 부호에 기반하여 주석을 나누는데, 이로 인해 각 줄이 60-70자 근처에 위치할 수 있습니다.

주석 길이가 80자를 초과하는 기존의 코드가 많이 있습니다. 이 지침은 가독성 검토의 일환으로 이러한 코드를 변경할 정당한 이유로 사용되어서는 안 됩니다(일관성 참조). 그러나 팀은 기회가 있을 때 다른 리팩토링과 함께 이 지침을 따르도록 주석을 업데이트하는 것을 권장합니다. 이 가이드라인의 주요 목표는 모든 Go 가독성 관리자가 권장 사항이 제시될 경우 동일한 권장 사항을 제시하도록 하는 것입니다.

**[The Go Blog의 documentation 포스트](https://blog.golang.org/godoc-documenting-go-code)**에서 주석에 대해 자세히 알아볼 수 있습니다.

```
# Good:
// This is a comment paragraph.
// The length of individual lines doesn't matter in Godoc;
// but the choice of wrapping makes it easy to read on narrow screens.
//
// Don't worry too much about the long URL:
// https://supercalifragilisticexpialidocious.example.com:8080/Animalia/Chordata/Mammalia/Rodentia/Geomyoidea/Geomyidae/
//
// Similarly, if you have other information that is made awkward
// by too many line breaks, use your judgment and include a long line
// if it helps rather than hinders.
```

작은 화면에서 반복적으로 줄 바꿈되는 주석은 독자에게 나쁜 사용자 경험을 제공합니다.

```
# Bad:
// This is a comment paragraph. The length of individual lines doesn't matter in
Godoc;
// but the choice of wrapping causes jagged lines on narrow screens or in code
review,
// which can be annoying, especially when in a comment block that will wrap
repeatedly.
//
// Don't worry too much about the long URL:
// https://supercalifragilisticexpialidocious.example.com:8080/Animalia/Chordata/Mammalia/Rodentia/Geomyoidea/Geomyidae/
```

### **Doc comments (문서 주석)**

모든 최상위 exported names은 doc 주석을 가져야 합니다. 의미나 동작이 명확하지 않은 unexported type이나 함수 선언도 doc 주석을 가져야 합니다. 이러한 주석은 해당 객체의 이름으로 시작하는 완전한 문장이어야 합니다. "a", "an", "the"와 같은 불문 유무를 이름 앞에 사용하여 자연스럽게 읽히도록 할 수 있습니다.

```
// Good:
// A Request represents a request to run a command.
type Request struct { ...

// Encode writes the JSON encoding of req to w.
func Encode(w io.Writer, req *Request) { ...
```

Doc 주석은 Godoc에서 표시되며 IDE에서도 나타나므로 패키지를 사용하는 사람을 위해 작성되어야 합니다.

문서 주석은 다음 심볼에 적용되거나, 구조체에서 나타나는 경우 해당 필드 그룹에 적용됩니다.

```
// Good:
// Options configure the group management service.
type Options struct {
    // General setup:
    Name  string
    Group *FooGroup

    // Dependencies:
    DB *sql.DB

    // Customization:
    LargeGroupThreshold int // optional; default: 10
    MinimumMembers      int // optional; default: 2
}
```

**Best Practice**: unexported code에 대한 문서 주석이 있는 경우 내보낸 것처럼 동일한 사용자 정의를 따르세요 (즉, 내보내지 않은 이름으로 주석을 시작하면 됩니다). 이렇게 하면 주석과 코드 양쪽에 있는 unexported name을 내보낼 때 간단히 교체하여 나중에 내보낼 수 있습니다.

### **Comment sentences (주석 문장)**

완전한 문장인 주석은 표준 영어 문장과 같이 대문자로 시작하고 마침표로 끝나야 합니다. (예외적으로 문장이 명확한 경우 대문자로 시작하지 않아도 됩니다. 이러한 경우는 아마도 단락의 시작 부분에서만 사용하는 것이 가장 좋습니다.)

문장 조각인 주석은 문장 부호나 대문자화에 대한 요구 사항이 없습니다.

**[Documentation comments](https://google.github.io/styleguide/go/decisions#doc-comments)**은 항상 완전한 문장이어야 하며, 이에 따라 대문자화와 문장 부호를 사용해야 합니다. 구조체 필드의 간단한 행 끝 주석은 필드 이름을 주제로 가정하는 간단한 구문일 수 있습니다.

```
// Good:
// A Server handles serving quotes from the collected works of Shakespeare.
type Server struct {
    // BaseDir points to the base directory under which Shakespeare's works are stored.
    //
    // The directory structure is expected to be the following:
    //   {BaseDir}/manifest.json
    //   {BaseDir}/{name}/{name}-part{number}.txt
    BaseDir string

    WelcomeMessage  string // displayed when user logs in
    ProtocolVersion string // checked against incoming requests
    PageLength      int    // lines per page when printing (optional; default: 20)
}
```

### **예제**

패키지는 사용 방법을 명확하게 문서화해야 합니다. 실행 가능한 예제를 제공하려고 노력하세요. 예제는 Godoc에 표시됩니다. 실행 가능한 예제는 테스트 파일에 포함되어야 하며, 제품 소스 파일에는 포함되어서는 안 됩니다. 예시로는 **[Godoc](https://pkg.go.dev/time#example-Duration)**와 **[source](https://cs.opensource.google/go/go/+/HEAD:src/time/example_test.go)**를 참조하세요.

실행 가능한 예제를 제공하기가 어려운 경우, 코드 주석 내에 예제 코드를 제공할 수 있습니다. 다른 코드 및 명령 줄 스니펫과 마찬가지로 표준 형식 규칙을 따라야 합니다.