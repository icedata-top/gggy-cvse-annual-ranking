# gggy-cvse-annual-ranking
瓜果瓜秧电视台&amp;CVSE旗下，Bilibili VOCALOID·UTAU分区新曲排行榜（简称年榜）制作程序。


## 简介

瓜果瓜秧电视台、CVSE 联合出品的[《虚拟歌手新曲排行榜》](https://space.bilibili.com/323657769/lists/1045155?type=season)，是以客观数据介绍 Bilibili VOCALOID·UTAU 分区每年新曲的可视化视频排行榜。该排行榜自 2019 年开始制作，目前包含“精选版”和“完整版”两个版本。

## 数据源

历史上，该排行榜使用 [天钿Daily](https://tdd.bunnyxt.com/) 的数据作为数据源。自 2024 年度起，使用冰数据作为数据源。具体地，涉及到数据表
* `video_static`
* `video_daily`
* `dim_user`
等。

在冰数据数据库的查询伪代码为：
```sql
SELECT *, (`view` + (favorite / ${修正a}) + (`like` / ${修正b}) ) AS `point`
FROM video_dynamic
    JOIN video_static ON video_dynamic.aid = video_static.aid
WHERE
    record_date = ${统计截止日期}
    AND YEAR(FROM_UNIXTIME(`pubdate`)) = ${年度}
    AND type_id = 30
ORDER BY `point` DESC
LIMIT 1000;
```

目前未对获取数据进行自动化，需要人工使用上述伪代码 SQL 查询出结果，然后导入 Excel （或腾讯文档在线表格），以方便下一步的手工收录。

## 制图

以[【年刊】2024 虚拟歌手新曲排行榜「精选版」](https://www.bilibili.com/video/BV1ovFpe7EQy)为例，在完成收录之后，需要从 Excel （或腾讯文档在线表格）中导出`.xlsx`文件，作为输入信息。旧版本的代码仅能完成主副榜制图。本仓库为新代码，设计实现以下内容的制图。

|内容板块|精选版|完整版|说明|
|--------|-|-|-|
|主榜|有|有|展示视频内容及数据|
|Pick Up|有|无|展示视频内容及数据，需要有Pick Up标注|
|副榜|有|有|展示视频封面及数据，每3个视频占1张图|
|前三全屏|有|有|透明底白字，前三名全屏时附在视频上方|
|前三过场|有|有|前三在主榜页之前的过场，展示封面及分数差|
|通用过场|有|有|视频中两板块之间的过场图|

因为副榜设计视频封面，需要有封面的缓存设计。

