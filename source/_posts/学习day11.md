---
title: 学习day11
date: 2025-11-24 12:59:01
tags: ["性能优化"]
---

# 后端缓存优化

```py
from django.utils.decorators import method_decorator
from django.views.decorators.cache import cache_page

class HotItemView(ListAPIView):
    serializer_class = HotItemSerializer
    pagination_class = HotItemPagination

    @method_decorator(cache_page(60 * 60))  # 缓存 1 小时
    def dispatch(self, *args, **kwargs):
        return super().dispatch(*args, **kwargs)

    def get_queryset(self):
        queryset = HotItem.objects.all()
        source = self.request.GET.get("source")
        category = self.request.query_params.get("category")

        if source:
            queryset = queryset.filter(source=source)
        if category:
            queryset = queryset.filter(category=category)

        return queryset.order_by("rank")

```

dispatch 是入口函数，通过 request.method 判断应该走 get 还是 post 之类的方法，所以装饰器都应该装到这个函数上面，cache_page 能根据的时间，缓存页面，当参数方法相同的时候，就会返回缓存的页面

render 上面的 key-value 服务对应就是后端的缓存，用的就是 redis

## cache 配置

```py
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": os.getenv("REDIS_URL"),
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}
```

```py
# 开发环境缓存：本地内存
    CACHES = {
        "default": {
            "BACKEND": "django.core.cache.backends.locmem.LocMemCache",
        }
    }
```

默认用的本地缓存

post 和 put 这些方法不能用缓存
