
# LAB2 設定CI/CD Pipeline
在此LAB我們將自動化程式碼集成、佈署的流程，打造一條自動化且包含LAB1之單元測試、程式碼品質掃描、建置、佈署之流程。 

![Image lab-env](https://raw.githubusercontent.com/j3ffk3/nccu-lab-2020/main/imgs/build-success.PNG)

## 設定 Pipeline (2 分)
1.利用文字編輯器打開 recommendation\src\main\resources目錄下既有的 Jenkinsfile  
2.修改 Stage1 Clone Source Code 下的 url 參數改為你git的位置，範例如下
```
stage('Clone Source Code') {
  steps {
            	echo 'Clone Source Code ...'
		git branch: 'main', url: '${GIT_URL}'
         }
}
```
3.查看 Stage2 Unit Test 下的指令，為LAB1單元測試下的指令
```
// Stage2 Unit Test
stage('Unit Test') {
	steps {
        	dir("recommendation") {
			sh """
				mvn test
			"""
		}
	}
}
```
4.查看 Stage3 Code Quality Analysis ，為LAB1程式碼品質分析下的指令，取代${STUDENT_ID}的部分為你的學號。
```
// Stage3 Code Quality Analysis
stage('Code Quality Analysis') {
	steps {
		dir("recommendation") {
			sh """
				mvn clean package sonar:sonar -Dsonar.projectKey=recommendation-${STUDENT_ID} -Dsonar.projectName=recommendation-${STUDENT_ID}
			"""
		}
	}
}
```
5.查看 Stage4Build Jar file ，為LAB1 編譯Java 的指令。
```
// Stage4 Build Jar file
stage('Build Jar file') {
	steps {
            	dir("recommendation") {
			sh """
				mvn clean package -DskipTests
			"""
		}
	}
}
```
6.查看 Stage5 Build Image & Deploy ，為LAB1 佈署應用的指令，取代${STUDENT_ID}的部分為你的學號。

```
// Stage5 Build Image & Deploy
stage('Build Image & Deploy') {
	steps {
            	dir("recommendation/target") {
			sh """
				oc project ${STUDENT_ID}
				oc start-build bc/recommendation --from-file recommendation-0.0.1-SNAPSHOT.jar -F
			"""
		}
	}
}
```
7.利用文字編輯器打開 recommendation\pipeline.yaml，將${GIT_URL}取代為你git的位置後存檔。
```
kind: "BuildConfig"
apiVersion: "build.openshift.io/v1"
metadata:
  name: "recommendation-pipeline"
spec:
  source:
    contextDir: recommendation/src/main/resources/
    git:
      uri: "${GIT_URL}"
      ref: "main"
  strategy:
    jenkinsPipelineStrategy:
      type: JenkinsPipeline
      jenkinsfilePath: Jenkinsfile
```
8.於 OpenShift 上建立 Pipeline
```
oc create -f pipeline.yaml
```
9.進入OpenShift 點選 start build 按鈕。
![Image lab-env](https://raw.githubusercontent.com/j3ffk3/nccu-lab-2020/main/imgs/lab2-start-build.PNG)
10.可以看到流程正常執行完畢。 
![Image lab-env](https://raw.githubusercontent.com/j3ffk3/nccu-lab-2020/main/imgs/build-success.PNG)

## 設定Webhook (1 分)
再有Pipeline的情況下上版還是需要用手去按，整合Webhook後在程式碼異動後將自動觸發流程，進一步加速團隊的開發協作速度。
1.執行以下指令為pipeline 加入 webhook
```
oc set triggers bc/recommendation-pipeline --from-webhook
```
2.你將會拿到啟動Pipeline的API，嘗試在利用Postman 上利用POST Method 呼叫，你會發現Pipeline被啟動了
```
oc describe bc/recommendation-pipeline
```
![Image lab-env](https://raw.githubusercontent.com/j3ffk3/nccu-lab-2020/main/imgs/lab2-webhook1.PNG)
3.將此API設定至GitHub，GitHub會於程式碼有異動時自動呼叫Pipeline的API啟動CI/CD流程。
至github 點選設定 > webhook 貼上步驟1得到的url，即完成設定。
![Image lab-env](https://raw.githubusercontent.com/j3ffk3/nccu-lab-2020/main/imgs/lab2-webhook2.PNG)

4.驗證
嘗試把RecommendationCtl.java 中的getRecommandateCreditCard() method中的回傳筆數由3改成5，看看CI/CD Pipeline 流程是否正常啟動。 

