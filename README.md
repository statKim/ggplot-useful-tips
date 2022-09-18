# 내가 가끔 쓰는 유용한 ggplot 코드 모음

## 기본 세팅
```r
library(tidyverse)
```

## theme 옵션들
```r
theme(
  # title 제목 가운데 정렬
  plot.title = element_text(hjust = 0.5),
  # legend 제목 없애기
  legend.title = element_blank(),
  # legend text size
  legend.text = element_text(size = 12),
  # legend 위치 (bottom, top 등)
  legend.position = "bottom"
)
```

## ggplot 여려 개 그리기
```r
# p1, p2는 각각 ggplot object
gridExtra::grid.arrange(p1, p2, 
                        nrow = 1)
```


## common legend 사용하기
```r
# 아래의 패키지 필요!!
# install.packages("ggpubr")
library(ggpubr)

fig_list <- list()   # ggplot object 넣어둘 list
for (i in 1:3) {
    # 각자 데이터에 맞도록 개별 그림 그리기
    p <- ggplot(
        data = df, 
        aes(
            x = time,
            y = val,
            group = var_name,
            color = var_name
        )
    ) +
        geom_line() +
        labs(x = "Days", y = "") +
        theme_bw() +
        theme(plot.title = element_text(hjust = 0.5),
              legend.title = element_blank(),
              legend.text = element_text(size = 12),
              legend.position = "bottom")
              
    # fig_list에 append
    fig_list <- c(fig_list,
                  list(p))
}

# 아래 함수로 common legend를 가지는 그림 그리기
ggpubr::ggarrange(plotlist = fig_list, 
                  nrow = 1, ncol = 3,
                  common.legend = TRUE, legend = "bottom")
```


## 지도 위에 그리기
```r
# 지도 그리는데 필요한 library
library(sf)
library(rnaturalearth)
library(rnaturalearthdata)

world <- ne_countries(scale = "medium", returnclass = "sf")
map_bg <- ggplot(data = world) +
    geom_sf() +
    coord_sf(xlim = range(bird$lon) + c(-5, 5),
             ylim = range(bird$lat) + c(-5, 5),
             expand = FALSE)
map_bg +
    geom_path(
        data = bird,
        aes(
            x = lon,
            y = lat,
            group = id,
            color = season
        ),
        size = 0.3
    ) +
    labs(x = "Longitude", y = "Latitude", color = "Season") +
    theme_bw()
```
