# Tips

### echo使用
最近在使用echo做web开发，发现它除了性能好，开发使用简单，对新增的路由做测试更是很方便
首先创建个简单的HTTP Server
```Go
package main

import (
	"net/http"
	"github.com/labstack/echo/v4"
)

func main() {
	e := echo.New()
	e.GET("/get", GetMethod)
	e.POST("/post", PostMethod)
	
	e.Logger.Fatal(e.Start(":1323"))
}

func GetMethod(c echo.Context) error{
    return c.String(http.StatusOK, "hello")
}

func PostMethod(c echo.Context) error{
    return c.JSON(http.StatusOK, {})
}
```

这里的路由处理函数，甚至可以在别的包中，只要它接收一个echo.Context类型作为参数，返回一个error类型即可。

接下来就是对接口做测试，echo提供了非常方便的方式来测试接口路由，这里分别对GET和POST两种请求方式做测试

#### GET测试
```Go
func TestQuicGetHostinfo(t *testing.T) {
	e := echo.New()
	q := make(url.Values)
	q.Set("ip", "192.168.0.1")
	req := httptest.NewRequest(echo.GET, "/?"+q.Encode(), nil)
	rec := httptest.NewRecorder()
	c := e.NewContext(req, rec)

	if GetMethod(c) != nil {
		t.Error("get hostinfo err")
	}

	t.Log(rec.Body.String())
}

```
接口请求成功时，测试函数就能正常的打印出响应信息

#### POST测试
```Go
func TestAssignRoute(t *testing.T) {
	e := echo.New()
	data := `{"abc":123, "xyz": "test"}`
	req := httptest.NewRequest(echo.POST, "/fixedipop", strings.NewReader(data))
	req.Header.Set(echo.HeaderContentType, echo.MIMEApplicationJSON)
	rec := httptest.NewRecorder()
	c := e.NewContext(req, rec)

	if err := PostMethod(c); err != nil {
		t.Error(err)
		return
	}

	t.Log(rec.Body.String())
}
```
接口请求成功时，测试函数就能正常的打印出响应信息，这在开发阶段非常的方便

