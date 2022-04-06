# EFK Stack

<!-- <u><span style="font-size:24.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif">EFK</span></u> -->

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif">When running multiple services and applications on a Kubernetes cluster, a centralized, cluster-level logging stack can help you quickly sort through and analyze the heavy volume of log data produced by your Pods. One popular centralized logging solution is the Elasticsearch, Fluentd, and Kibana (EFK) stack.</span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<u><span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif">Elasticsearch</span></u> <span style="font-size:14.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif">is a real-time, distributed, and scalable search engine which allows for full-text and structured search, as well as analytics. It is commonly used to index and search through large volumes of log data but can also be used to search many different kinds of documents.</span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif">Elasticsearch is commonly deployed alongside <u>Kibana</u>, a powerful data visualization frontend and dashboard for Elasticsearch. Kibana allows you to explore your Elasticsearch log data through a web interface and build dashboards and queries to quickly answer questions and gain insight into your Kubernetes applications.</span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif">We use <u>Fluentd</u> to collect, transform, and ship log data to the Elasticsearch backend. Fluentd is a popular open-source data collector that we’ll set up on our Kubernetes nodes to tail container log files, filter and transform the log data, and deliver it to the Elasticsearch cluster, where it will be indexed and stored.</span>

<!-- <span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span> -->

<!-- <span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span> -->

<!-- <span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span> -->

<!-- <span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span> -->

<span style="font-size:14.0pt;
line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<u><span style="font-size:20.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif">EFK Architecture</span></u><span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif"></span>

<span style="font-size:14.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif;
mso-no-proof:yes">
![](https://i.imgur.com/WWzDXL1.png)
</span><span style="font-size:14.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif"></span>

<span style="font-size:14.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif"> </span>

<span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif">EKF components get deployed as follows:</span>



<span style="font-size:14.0pt;line-height:107%;font-family:Symbol;mso-fareast-font-family:
Symbol;mso-bidi-font-family:Symbol">·<span style="font-variant-numeric: normal; font-variant-east-asian: normal; font-stretch: normal; font-size: 7pt; line-height: normal; font-family: &quot;Times New Roman&quot;;"></span></span> <span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif">Fluentd: - Deployed as daemonset as it need to collect the container logs from all the nodes. It connects to the Elasticsearch service endpoint to forward the logs.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:Symbol;mso-fareast-font-family:
Symbol;mso-bidi-font-family:Symbol">·<span style="font-variant-numeric: normal; font-variant-east-asian: normal; font-stretch: normal; font-size: 7pt; line-height: normal; font-family: &quot;Times New Roman&quot;;"></span></span> <span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif">Elasticsearch: - Deployed as statefulset as it holds the log data. We also expose the service endpoint for Fluentd and Kibana to connect to it.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:Symbol;mso-fareast-font-family:
Symbol;mso-bidi-font-family:Symbol">·<span style="font-variant-numeric: normal; font-variant-east-asian: normal; font-stretch: normal; font-size: 7pt; line-height: normal; font-family: &quot;Times New Roman&quot;;"></span></span> <span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif">Kibana: - Deployed as deployment and connects to elasticsearch service endpoint.</span>
<br>
<br>
<span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif"> </span>
![](https://i.imgur.com/UMV2Lti.png)
<span style="font-size:14.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif"></span>

<u><span style="font-size:20.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif">Kubernetes StatefulSets</span></u>

<span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif">StatefulSet is the workload API object used to manage stateful applications.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif">Manages the deployment and scaling of a set of Pods and provides guarantees about the ordering and uniqueness of these Pods.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif">Like a Deployment, a StatefulSet manages Pods that are based on an identical container spec. Unlike a Deployment, a StatefulSet maintains a sticky identity for each of their Pods. These pods are created from the same spec but are not interchangeable: each has a persistent identifier that it maintains across any rescheduling.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif">If you want to use storage volumes to provide persistence for your workload, you can use a StatefulSet as part of the solution. Although individual Pods in a StatefulSet are susceptible to failure, the persistent Pod identifiers make it easier to match existing volumes to the new Pods that replace any that have failed.</span>

![](https://i.imgur.com/PEiu0Q6.png)

<!-- <span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif;mso-no-proof:yes">![Diagram

Description automatically generated](file:///C:/Users/VAIBHA~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)</span><span style="font-size:14.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif"></span> -->

<!-- <span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif;mso-no-proof:yes">![Diagram

Description automatically generated](file:///C:/Users/VAIBHA~1/AppData/Local/Temp/msohtmlclip1/01/clip_image003.png)</span><span style="font-size:14.0pt;line-height:107%;font-family:&quot;Times New Roman&quot;,serif"></span> -->

![](https://i.imgur.com/YMlCMtx.png)

<span style="font-size:14.0pt;line-height:107%;font-family:
&quot;Times New Roman&quot;,serif">StatefulSets are valuable for applications that require one or more of the following.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:Symbol;mso-fareast-font-family:
Symbol;mso-bidi-font-family:Symbol">·<span style="font-variant-numeric: normal; font-variant-east-asian: normal; font-stretch: normal; font-size: 7pt; line-height: normal; font-family: &quot;Times New Roman&quot;;"></span></span> <span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif">Stable, unique network identifiers.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:Symbol;mso-fareast-font-family:
Symbol;mso-bidi-font-family:Symbol">·<span style="font-variant-numeric: normal; font-variant-east-asian: normal; font-stretch: normal; font-size: 7pt; line-height: normal; font-family: &quot;Times New Roman&quot;;"></span></span> <span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif">Stable, persistent storage.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:Symbol;mso-fareast-font-family:
Symbol;mso-bidi-font-family:Symbol">·<span style="font-variant-numeric: normal; font-variant-east-asian: normal; font-stretch: normal; font-size: 7pt; line-height: normal; font-family: &quot;Times New Roman&quot;;"></span></span> <span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif">Ordered, graceful deployment and scaling.</span>

<span style="font-size:14.0pt;line-height:107%;font-family:Symbol;mso-fareast-font-family:
Symbol;mso-bidi-font-family:Symbol">·<span style="font-variant-numeric: normal; font-variant-east-asian: normal; font-stretch: normal; font-size: 7pt; line-height: normal; font-family: &quot;Times New Roman&quot;;"></span></span> <span style="font-size:14.0pt;line-height:107%;
font-family:&quot;Times New Roman&quot;,serif">Ordered, automated rolling updates.</span>
