# ShenzhenHouseInfoCrawler
## 目的
- 分析深圳楼市趋势

## 主要功能
### 抓取数据
- 抓取深圳楼市数据，每天从[深圳房地产信息网](http://ris.szpl.gov.cn/default.aspx)中抓取以下信息
    - 每日的[一手房成交信息](http://ris.szpl.gov.cn/credit/showcjgs/ysfcjgs.aspx?cjType=0)
    - 每日的[二手房成交信息](http://ris.szpl.gov.cn/credit/showcjgs/esfcjgs.aspx)
    - 每日的[新房预售信息](http://ris.szpl.gov.cn/bol/)
    - 历史的[二手房源信息](http://ris.szpl.gov.cn/bol/essource.aspx)
    - 历史的[预售信息](http://ris.szpl.gov.cn/bol/)
- 抓取的数据存储在Postgresql数据数据库中，可以使用script.sql来创建数据库中对应的表；

### 已经抓取的数据
- 在data/loushi.backup文件中包含了以下数据：
    - 截止20180905，该系统内有记录的所有新房房源信息，3000多个预售项目，1万多栋建筑，100多万套房源的基本信息
    - 截止20180905，该系统有记录以来的所有二手房源信息
    - 包含从2018-6-20到2018-9-5的趋势信息，每天的新房成交、二手房成交
- 如果需要使用这部分数据，可以在pgadmin中创建名为loushi的数据库，然后在数据库名字上右键->恢复，选择该文件即可

### 分析数据
- 长期运行，分析历史趋势，用pandas、matplotlib对这些数据做各种维度的统计分析
- 暂未实现

### 发现与通知
- 每天查询最新的预售信息，发现新的预售就及时邮件通知相关人员，邮箱信息可以在config.ini文件中配置

## 依赖的库
- requests
- BeautifulSoap
- schedule

## 如何使用
- 安装postgresql数据库
- 在postgresql内创建名字为loushi的数据库
- 在loushi内，使用script/script.sql创建对应的表
- 修改src.Dao.Daobase.py中的数据库连接信息，设置正确的服务器地址和用户名密码
- 如果需要了解一手房源信息，则要配置main.py目录下的config.ini文件
- 运行main.py

## 更新记录
- 20180830
    -bug fix: 如果项目详情中没有楼栋信息，程序会异常退出
- 20180824
    - 修改读取一手房源项目详情的方式，由于历史项目较多，估计要好几天，甚至几十天才能抓取完，所以改为先抓取所有项目信息到数据库，
    然后再逐个抓取每个项目的详细信息，假如中间出现意外中断，重启程序即可断点续传。而且每抓取完一个项目信息之后，会检查下其他任务，
    避免中间其他任务等待时间过长
- 20180821
    - 添加功能，NewHSrcProjectCrawler，直接抓取所有一手房源的项目信息到数据库，不抓取详情
    - 添加功能，NewHouseMonitor，每小时检查下是否有新的房源通过预售，有的话，就通过config.ini配置信息给相关人员发送邮件通知
- 20180815
    - 重构了数据库接口，每种类型数据的接口提取成单独的类
- 20180814 
    - 支持抓取一手房源信息
    - 将main.py文件提取到根目录，便于pyinstaller生成exe文件
    - 使用scheduler调度库，每天中午查询一次，抓取相关一手、二手的房源和成交数据
      
## 待办事项
- 输入一个项目名称，生成该项目的报告：项目名称，地址，预售时间，几栋，几套、最高单价及其对应房源房号，最低单价及其对应房号，最高总价，最低总计，然后是所有房源信息列表，把该报告发送到指定邮箱
- 统计分析报告，分析一手、二手房成交的历史趋势
- 使用sqlalchemy来改造这里的dao部分，目前这个太原始了
- 使用pipenv来管理依赖
- 使用gitignore文件来管理版本
- 使用python-dotenv来管理环境变量
