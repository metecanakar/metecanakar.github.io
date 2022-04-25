---
layout: post
title: (TR) PySpark Performans Optimizasyonu
subtitle: Pyspark Performance Optimization in Turkish
tags: [Pyspark, Performance, Optimization]
comments: true
---




**Bu blogda Spark'ı daha performanslı kullanabilmek için yapılabilecek işlemleri anlatmaya çalışacağım.**

## Query Planı Anlamak
Spark API'sini kullanarak yazdığınız bütün kodlar Spark'ın lazy evaluation mekanizmasından dolayı bir DAG (Directed acyclic graph ya da Türkçe yönlendirilmiş döngüleri olmayan yönlendirilmiş grafik)' a dönüştürülerek çalıştırıl. Bu DAG' ı anlamak için örneğin my_df.explain(extended=True) metodunu kullanarak query planı görebilirsiniz. Bu query planda hangi tip join yaptığınız vs. herşey açıkca görülebilir.
Alternatif olarak Spark UI'daki her bir Stage altında DAG Visualization sekmesine tıklarsanız, UI'da da aynı şekilde query planı görebilirsiniz. İkisi de yaklaşık olarak benzer işe yarar ve kodunuzu ona göre optimize edebilirsiniz. 


## To cache or not to cache?

Eğer bir dataframe' i birden fazla kez kullanacaksanız, o dataframe'i cachelemek tavsiye edilmektedir.  Eğer cache yapılmazsa ve aynı dataframe'i birden fazla kez kullacaksanız spark aynı işlemi tekrar tekrar yapar (lazy execution'dan dolayı).
Caching ya da Persisting
Cache (only in memory)
Persisting (storage level' ı ayrıca ayarlanabilir e.g. disk ya da memory)
Cache edilen Dataframe ile işiniz bittiyse, derhal uncache ya da unpersist edilmeli (memoryde yer açmak için)
 Eğer çok fazla persist yaparsanız (overpersist), extra spill to disk gözlenebilir.
Dataframe Cache yaparken dikkat edilmesi gereken bir diğer nokta da cache() ve persist() transformation oldukları için bu dataframe sadece cache için işaretlenicek. Bu dataframe'in gerçekten cachelenmesi için bir action çağırılması gerekir ÖR: count(), show() gibi.


## Skewed Data in Partitions (Dengesiz Partitionlar)
Bu sorun parameter tuning ile çözülemez. Kodda değişiklik gerekir!
Kodda yapılabilecek değişiklikler:
Broadcast Join Kullanmak: Eğer veriniz herhangi bir sebepten dolayı (dengesiz Join gibi ör: çok büyük tablo ile küçük tablonun joini) partitionlara dengesiz dağılırsa. Bu durumda sortmerge join yerine broadcast join kullanılmalı. 
large_df.join(F.broadcast(small_df), large_df.keycolumn== small_df.keycolumn)
Partitionların dengesiz olup olmadığını anlamak için ise aşağıdaki kontrol yapılabilir:
my_df.groupBy(F.spark_partition_id()).count().show(1600) # we assume that we set 1600 shuffle partitions
Salted Key kullanmak
Partition key kullanmak yerine, random bir numara kullanarak eşit partition sağlamak ve daha sonrasında orjinal key'e geri dönmek. (bu şekilde daha az veri işe uğraşabiliriz)
Daha iyi bir partition key kullanmak.

## Filter Trick
Her zaman joinden önce filter etmek joinlenecek data size'ını azalttığı için önemlidir.
