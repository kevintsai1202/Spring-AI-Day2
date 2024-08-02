平常使用 ChatGPT 或其他類似的對話式 AI 都可直接使用，大不了額度沒了會降為效果比較差的模型，不過要進行程式開發就需要先申請 API key，除非使用本地的 LLM。

原本凱文大叔測試 AI 都以本地 LLM 或是一些免費的服務為主，不過 2024/7/18 [OpenAI 發布 GPT-4o mini](https://openai.com/index/gpt-4o-mini-advancing-cost-efficient-intelligence/) 模型後就完全改觀了，價格竟然比 GPT-3.5 Turbo 還便宜 60%，花 5 美金就可以用很久(Open AI 最低額度)，平時想省點費用還可以改用尚未收費的 [groq](https://console.groq.com)，這也會讓原本猶豫不決的開發人員一起加入 AI 的賽道，接下來就跟大家說這把鑰匙在哪創建

## Open AI:

原本 Open AI 個人的 API key 是在 Profile 下創建，不過前陣子已改為在 Project 下建立

首先進入 [Open AI](https://openai.com/) 後臺管理介面
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290apeeSQnJDZ.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290apeeSQnJDZ.png)

登入後點選 Dashboard→API Keys→Create new secret key 就能建立 API key
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290BT08cfgzNC.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290BT08cfgzNC.png)


名稱可以隨便取，預設會有一個 Default project，若有多個專案也可以建立好 Project 再來建立 API key，有特別權限需求可以在 Permissions 設定，記得產生 API key 後要馬上複製，不然視窗關閉後就無法取得完整的 API key ，只能重新建立一組
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290gsRpyLd5Y0.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290gsRpyLd5Y0.png)
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290f2pGObtkJI.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290f2pGObtkJI.png)

> API key 記得保存好也別洩漏出去，不然額度怎麼被用完都不曉得

## groq:

groq 建立 key 的方式也非常簡單，在[主頁](https://groq.com/)點選 GroqCloud for Developers，即可進入後臺
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290yWEVGYKIJR.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290yWEVGYKIJR.png)

接著點選 API Keys→Create API Key 輸入 Key name 就能產生 API key
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290cu3vSO0CoQ.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290cu3vSO0CoQ.png)![https://ithelp.ithome.com.tw/upload/images/20240801/20161290SIPY5U0tJQ.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290SIPY5U0tJQ.png)

> 與 Open AI 一樣沒複製就查不到了

有了入門鑰匙就能開始創建 Spring 專案，凱文大叔使用的 IDE 是 Spring 官方提供的 [Spring Tools 4 for Eclipse](https://spring.io/tools)，免費又好用，許多功能都直接整合進 IDE

先點選 File → New → Project，接著選擇 Spring Starter Project
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290ZGygob4JoO.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290ZGygob4JoO.png)![https://ithelp.ithome.com.tw/upload/images/20240801/20161290MlXNSovjmu.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290MlXNSovjmu.png)


專案相關名稱填寫完畢就可以勾選需要的依賴，目前 Java Version 只能選到 17 以後的版本，Spring Boot Version 也只能選擇 3.2.8 以後，若要使用以前的版本就只能建立 Maven 專案自行撰寫 pom.xml 了

![https://ithelp.ithome.com.tw/upload/images/20240801/201612905XYnrzLoKq.png](https://ithelp.ithome.com.tw/upload/images/20240801/201612905XYnrzLoKq.png)![https://ithelp.ithome.com.tw/upload/images/20240801/2016129065EN0h2R4x.png](https://ithelp.ithome.com.tw/upload/images/20240801/2016129065EN0h2R4x.png)

凱文大叔勾選的依賴有

- OpenAI: 我們的主角
- Spring Boot DevTools: 開發 Web 程式時修改 Class 內容會自動重啟 Tomcat
- Lombok: 透過標註簡化 Class 的許多方法，例如 Setter / Getter / Constructor 都能自動生成
- Spring Web: 撰寫 API 的必備依賴

專案建立完成後先執行看看
![https://ithelp.ithome.com.tw/upload/images/20240801/20161290rMjLePwFJQ.png](https://ithelp.ithome.com.tw/upload/images/20240801/20161290rMjLePwFJQ.png)

竟然直接報錯，看一下錯誤的 log

> org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'openAiChatModel' defined in class path resource [org/springframework/ai/autoconfigure/openai/OpenAiAutoConfiguration.class]: Failed to instantiate [org.springframework.ai.openai.OpenAiChatModel]: Factory method 'openAiChatModel' threw exception with message: OpenAI API key must be set

原來是 Spring Boot 的自動配置找不到 API key，趕快把剛剛創建的 key 設定在 application.yml 中，凱文大叔習慣有結構性的 yml 設定，直接把 src/main/resources 下的 application.properties 附檔名改掉即可，接著按照下面的格式把 API key 填上

```yaml
spring:
  ai:
    openai:
      api-key: {輸入剛剛創建的 API key}
      chat:
        options:
          model: gpt-4o-mini  #記得把模型改為 gpt-4o-mini 才能享受便宜快速的模型
```

再執行一次程式，這次順利啟動沒任何錯誤，表示自動配置沒問題，Spring 容器會自動幫我們建立 openAiChatModel 的 Bean
![https://ithelp.ithome.com.tw/upload/images/20240801/201612909K9m7zEAzK.png](https://ithelp.ithome.com.tw/upload/images/20240801/201612909K9m7zEAzK.png)

Open AI 若還沒儲值也能先創建 API key，程式到這都沒問題，不過接下來跟 AI 對話若沒額度就會失敗了，想先體驗 AI 的朋友也可以改用 groq，設定如下

```yaml
spring:
  ai:
    openai:
      api-key: {輸入 groq 創建的 API key}      
      base-url: https://api.groq.com/openai 
      #groq與open ai相容，其他設定都一樣只需增加 base-url 即可
      chat:
        options:
          model: llama-3.1-70b-versatile #這裡要輸入 groq 提供的模型
```

總結一下今天學到了甚麼?

- 如何取得 API key
- 在 Spring Tool 4 建立一個 AI 專案
- 將 API key 設定在 application.yml 並成功啟動程式

> 題外話沉浸式翻譯可以自訂相容於 OpenAI 的 API，我們可以把 groq 的 API 設上去就能免費使用 AI 翻譯囉
