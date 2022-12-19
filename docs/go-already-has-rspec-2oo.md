# Go 已经有 RSpec 了！

> 原文：<https://dev.to/alirezabashiri/go-already-has-rspec-2oo>

你可以使用`(*testing.T).Run(string, func(t *testing.T))`来单独运行每个测试用例，并且比表格驱动的方法更具可读性。

```
import (
    "testing"
    "github.com/stretchr/testify/assert"
)

func TestRateRecipe(t *testing.T) {
    t.Run("ok", func(t *testing.T) {
        res := post("/recipes/1/rating", nil)
        assert.Equal(t, res.Code, http.StatusOK)
    })

    t.Run("id is invalid", func(t *testing.T) {
        res := post("/recipes/foo/rating", nil)
        assert.Equal(t, res.Code, http.StatusBadRequest)
    })
} 
```