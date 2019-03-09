### chi
---
https://github.com/go-chi/chi

```go
package main

import (
  "net/http"
  "github.com/go-chi/chi"
)

func main() {
  r := chi.NewRouter()
  r.Get("/", func(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("welcome"))
  })
  http.ListenAndServe(":3000", r)
}


import (
  "context"
  "github.com/go-chi/chi"
  "github.com/go-chi/chi/middleware"
)

func main() {
  r := chi.NewRouter()
  
  r.Use(middleware.RequestID)
  r.Use(middleware.RealIP)
  r.Use(middleware.Logger)
  r.Use(middleware.Recoverer)
  
  r.Use(middleware.Timeout(60 * time.Second))
  
  r.Get("/", func(w http.ResponseWritr, r *http.Request) {
    w.Write([]byte("hi"))
  })
  
  r.Route("/articles", funcs(r chi.Router) {
    r.With(paginate).Get("/", listArticles)
    r.With(paginate).Get("/{month}-{day}-{year}", listArticlesByDate)
    
    r.Post("/", createArticle)
    r.Get("/search", searchArticles)
    
    r.Get("/{articleSlug:[a-z-]+}", getArticleBySlug)
    
    r.Route("/{articleID}", func(r chi.Router) {
      r.Use(ArticleCtx)
      r.Get("/", getArticle)
      r.Put("/", updateArticle)
      r.Delete("/", deleteArticle)
    })
  })
  
  r.Mount("/admin", adminRouter())
  
  http.ListenAndServe(":3333", r)
}

func ArticleCtx(next http.Handler) http.Handler {
  return http.HandlerFunc(func(w http.ResposeWriter, r *http.Request) {
    articleID := chi.URLParam(r, "articleID")
    article, err := dbGetArticle(articleID)
    if err != nil {
      http.Error(w, http.StatusText(404), 404)
      return
    }
    ctx := context.WithValue(r.Context(), "article", article)
    next.ServeHTTP(w, r.WithContext(ctx))
  })
}

func getArticle(w http.ResponseWriter, r *http.Request) {
  ctx := r.Context()
  article, ok := ctx.Value("article").(*Article)
  if !ok {
    http.Error(w, http.StatusText(422), 422)
    return
  }
  w.Write([]byte(fmt.Sprintf("title:%s", article.Title)))
}

func adminRouter() http.Handler {
  r := chi.NewRouter()
  r.Use(AdminOnly)
  r.Get("/", adminIndex)
  r.Get("/accounts", adminListAccounts)
  return r
}

func AdminOnly(next http.Handler) http.Handler {
  return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()
    perm, ok := ctx.Value("acl.permission").(YourPermissionType)
    if !ok || !perm.IsAdmin() {
      http.Error(w, http.StatusText(403), 403)
      return
    }
    next.ServeHTTP(w, r)
  })
}


type Router interface {
  http.Handler
  Routes
  
  Use(middlewares ...func(http.Handler) http.Handler)
  
  With(middlewares ...func(http.Handler) http.Handler) Router
  
  Group(fn func(r Router)) Router
  
  Route(pattern string, fn func(r Router)) Router
  
  Mount(pattern string, h http.Handler)
  
  Handle(pattern string, h http.Handler)
  HandleFunc(pattern string, h http.HandlerFunc)
  
  Method(method, pattern string, h http.Handler)
  MethodFunc(method, pattern string, http.HandlerFunc)
  
  Connect(pattern string, h httpHandlerFunc)
  Delete(pattern stirng, h httpHandlerFunc)
  Get(pattern string, h httpHandlerFunc)
  Head(pattern string, h http.HandlerFunc)
  Options(pattern string, h http.HandlerFunc)
  Patch(pattern string, h http.HandlerFunc)
  Post(pattern string, h http.HandlerFunc)
  Put(pattern string, h http.HandlerFunc)
  Trace(pattern string, h http.HandlerFunc)
  
  NotFound(h http.HandlerFunc)
  
  MethodNotAllowed(h http.HandlerFunc)
}

type Routes interface {
  Routes() []Route
  
  Middlewares() Middlewares
  
  Match(rctx *Context, method, path string) bool
}

func MyMiddleware(next http.Handler) http.Handler {
  return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
    ctx := context.WithValue(r.Context(), "user", "123")
    next.ServeHTTP(w, r.WithContext(ctx))
  })
}

func MyRequestHandler(w http.ResponseWriter, r *http.Reques) {
  user := r.Context().Value("user").(string)
  w.Write([]byte(fmt.Sprintf("hi %s", user)))
}

func MyRequestHandler(w http.ResponseWriter, r *http.Request) {
  userID := chi.URLParam(r, "userID")
  
  ctx := r.Context()
  key := ctx.Value("key").(string)
  
  w.Write([]byte(fmt.Sprintf("hi %v, %v", userID, key)))
}

```

```
```

```
```


