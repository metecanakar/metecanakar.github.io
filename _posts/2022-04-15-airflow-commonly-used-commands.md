---
layout: post
title: Apache Airflow Commonly Used Commands
subtitle: 
cover-img: /assets/img/uniknterasse.jpeg
thumbnail-img: /assets/img/seealpsee1.jpeg
share-img: /assets/img/seealpsee1.jpeg
tags: [apache, airflow]
---

As all of you know, Apache Airflow is used for scheduling and monitoring (at least I like to use it that way). In this blog post, I will share with you some commonly used commands.

<!-- wp:paragraph {"align":"justify"} -->
<p class="has-text-align-justify"><strong>In order to trigger a DAG which would run the DAG only once (start date and end date are same): </strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph {"align":"justify"} -->
<p class="has-text-align-justify"><code>airflow backfill -s 2020-10-28 -e 2020-10-28 DAGID</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph {"textColor":"vivid-red"} -->
<p class="has-vivid-red-color has-text-color">PS: If the -s -e parameters are not same, it will run multiple times. E.g. if the difference between the parameters -s and -e is 30 days, then it will run 30 times!</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>In order to trigger a specific task for a DAG:</strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>airflow backfill -t TASKID 2020-10-28 -e 2020-10-28 DAGID</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>In order to clear a specific task:</strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>airflow clear -t TASKID 2020-10-28 -e 2020-10-28 DAGID</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>Running only cleared tasks on the whole DAG without backfilling each task:</strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Just clear the tasks which we want to clear, then start the whole DAG for the same run date. Example command:</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>airflow backfill -s 2020-10-28 -e 2020-10-28 DAGID</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><strong>Starting and stopping airflow webserver:</strong></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>If there is an existing "Master Airflow Webserver" running, stop it with the below commands:</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>First, list the currently running webserver(s):</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>ps aux | grep webserver</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Second, after listing them, find the process ID of your "gunicorn: master [airflow-webserver]" and run the below command</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>kill PROCESSID (e.g. kill 5036)</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Then start the web server with your port number again:</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p><code>airflow webserver -p PORTNO</code></p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Open a new terminal while webserver is running and do your backfill on that terminal.</p>
<!-- /wp:paragraph -->
