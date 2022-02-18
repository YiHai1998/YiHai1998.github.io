# RESUFul

```python
// 不适用RESTful风格
r.GET("/create_book", ...)
r.GET("/update_book", ...)

r.GET("/remove_book", ...)	// 有的人删除
r.GET("/delete_book", ...)	// 有的人删除
r.GET("/shanchu_book", ...)	// 有的人删除

// 使用RESTful风格
r.GET("book", ...)
r.POST("book", ...)
r.PUT("book", ...)
r.DELETE("book", ...)
```

