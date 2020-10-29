
# recommendation (推薦微服務)

推薦微服務，基於Spring boot框架開發。本服務將呼叫credit-card(信用卡微服務)之API取得信用卡資料，依照每張信用卡之國內現金回饋、海外現金回饋、年費與其他優惠做評分，選出評分最高的3筆推薦給網頁之終端使用者。

推薦預設規則如下:
- 國內現金回饋(佔比25%):  

| 回饋百分比  | 得分 |
| ------------- | ------------- |
| 小於1%  | 0 |
| 1%-2%  | 50  |
| 2%以上  | 100  |

- 海外現金回饋(佔比25%):

| 回饋百分比  | 得分 |
| ------------- | ------------- |
| 小於2%  | 0 |
| 2%-3%  | 50  |
| 3%以上  | 100  |

- 年費(佔比25%):

| 年費  | 得分 |
| ------------- | ------------- |
| 2000以上  | 0 |
| 1-2000  | 50  |
| 0  | 100  |

- 其他優惠(佔比25%): 

| 其他優惠  | 得分 |
| ------------- | ------------- |
| 無  | 0 |
| 有其他優惠  | 100  |

## Lab2-1 體驗單元測試 (1分)
https://github.com/j3ffk3/nccu-lab-2020/blob/main/recommendation/LAB1.md
## Lab2-2 體驗程式碼品質分析 (1分)
https://github.com/j3ffk3/nccu-lab-2020/blob/main/recommendation/LAB1.md
## Lab2-3 體驗手動部署應用 (1分)
https://github.com/j3ffk3/nccu-lab-2020/blob/main/recommendation/LAB1.md
## Lab2-4 完成 Jenkinsfile (1分)
https://github.com/j3ffk3/nccu-lab-2020/blob/main/recommendation/LAB1.md
## Lab2-5 設定 Pipeline (1分)
https://github.com/j3ffk3/nccu-lab-2020/blob/main/recommendation/LAB1.md
## Lab2-6 設定 WebHook (1分)
https://github.com/j3ffk3/nccu-lab-2020/blob/main/recommendation/LAB1.md

## 小遊戲
- 依照組別討論一下，依照大家的生活經驗來說，以上四個評分項目的佔比是否有些可以提高，有些可以降低呢?
- 討論完成後，嘗試調整每個評分項目的百分比。
(修改RuleService裡面的getWeight method)
- 並將程式碼上傳至github.
- CI/CD流程將自動幫你集成/佈署最新的程式碼。
