# Docker 基礎概念 101

> Docker 想解決的問題：改善傳統虛擬機器因為需要額外安裝作業系統（Guest OS），導致啟動慢、佔較大記憶體的問題
>
> Docker 要提供的解法：以應用程式為核心虛擬化，取代傳統需要 Guest OS 的虛擬化技術

![Docker](https://www.docker.com/sites/default/files/social/docker_facebook_share.png)

在了解 Docker 前首先要先了解何謂虛擬化。

白話來說虛擬化要解決的問題就是：「我寫好了一支程式，在我的電腦上可以正常運作，但搬到你的電腦上可能就爆掉」。會有這樣的問題是因為：每台電腦的作業系統與硬體配置不盡相同，我的程式可能剛好只跟我電腦上的環境相容。而虛擬化要做的就是模擬出一個環境，讓程式可以在不同硬體上執行時，都以為自己在同一個環境中執行。

目前常見用來比較的虛擬化技術有兩種，一種是在系統層級的虛擬化技術，在這我們叫它稱虛擬機器（Virtual machine）。另外一種則是在作業系統層級，此稱容器（Container）。前者的代表如 Virtual Box，而後者如 Docker。

## 虛擬機器 vs 容器

### 虛擬機器（以作業系統為中心）

- 虛擬化的目標：將一個應用程式所需的執行環境打包起來，建立一個獨立環境，方便在不同的硬體中移動。

虛擬機器是在系統層上虛擬化，透過 Hypervisor 在目標的機器上提供可以執行一個或多個虛擬機器的平台。而這些虛擬機器可以執行完整的作業系統。簡單來說，Hypervisor 就是一個可以讓你在作業系統（Host OS）上面再裝一個作業系統（Guest OS），然後讓兩個作業系統彼此不會打架的平台。

透過選擇不同的 Guest OS，虛擬機器的技術就可以確保只要我的程式在該 Guest OS 上可以正常運作，那放到你的電腦上跑時，可以不管你的 Host OS 是什麼，只要在你的 Host OS 上先裝上我的  Guest OS，我的程式就可以正常在你的電腦上運作。

![VM](https://oer.gitlab.io/oer-on-oer-infrastructure/figures/OS/virtual-machines.png)

### 容器（以應用程式為中心）

- 容器化的目標：改善虛擬機器因為需要裝 Guest OS 導致啟動慢、佔較大記憶體的問題。

容器是在作業系統層上虛擬化，透過 Container Manager 直接將一個應用程式所需的程式碼、函式庫打包，建立資源控管機制隔離各個容器，並分配 Host OS 上的系統資源。透過容器，應用程式不需要再另外安裝作業系統（Guest OS）也可以執行。

因為不需要另外安裝作業系統，建立容器所需要的硬碟容量可以大幅降低，且啟動速度可以更快，不需要等待 Guest OS 的開機時間。

![Container](https://oer.gitlab.io/oer-on-oer-infrastructure/figures/OS/containers.png)



關於更多虛擬機器跟容器的利弊，請見：[Stack Overflow](https://stackoverflow.com/questions/16047306/how-is-docker-different-from-a-virtual-machine#)。



## Docker 三元素

在了解完 Docker 的基本概念後，接下來我們要進一步了解要使用 Docker 時最重要的三個元素：映像檔、容器、倉庫。 用一個簡單的比喻來解釋，如果映像檔是一個做蛋糕的模具，容器則是用該模具考出來的蛋糕，而倉庫是用來集中放置模具們的收納櫃。接下來讓我們搭配這個比喻深入一點解釋這三個概念：

### 映像檔 Image

Docker 映像檔是一個模板，用來重複產生容器實體。例如：一個映像檔裡可以包含一個完整的 MySQL 服務、一個 Golang 的編譯環境、或是一個 Ubuntu 作業系統。

透過 Docker 映像檔，我們可以快速的產生可以執行應用程式的容器。而 Docker 映像檔可以透過撰寫由命令行構成的 Dockerfile 輕鬆建立，或甚至可以從公開的地方下載已經做好的映像檔來使用。

舉例來說，如果我今天想要一個 node.js 的執行環境跑我寫好的程式，我可以直接到上 [DockerHub](https://hub.docker.com/) 找到相對應的 node.js 映像檔 ，而不需要自己想辦法打包一個執行環境。

### 容器 Container

就像是用蛋糕模具烤出來的蛋糕本體，容器是用映像檔建立出來的執行實例。它可以被啟動、開始、停止、刪除。每個容器都是相互隔離、保證安全的平台。

可以把容器看做是一個執行的應用程式加上執行它的簡易版 Linux 環境（包括 root 使用者權限、程式空間、使用者空間和網路空間等）。

另外要注意的是，Docker 映像檔是唯讀（read-only）的，而容器在啟動的時候會建立一層可以被修改的可寫層作為最上層，讓容器的功能可以再擴充。這點在下面的實例會有更多補充。

### 倉庫 Repository

倉庫（Repository）是集中存放映像檔檔案的場所，也可以想像成存放蛋糕模具的大本營。倉庫註冊伺服器（Registry）上則存放著多個倉庫。

最大的公開倉庫註冊伺服器是上面提到過的 [Docker Hub](https://hub.docker.com/)，存放了數量龐大的映像檔供使用者下載，我們可以輕鬆在上面找到各式各樣現成實用的映像檔。

而 Docker 倉庫註冊伺服器的概念就跟 Github 類似，你可以在上面建立多個倉庫，然後透過 push、pull 的方式上傳、存取。



## 建立你的第一個 Docker Image

在瞭解了 Docker 的基本元素後，就讓我們來手把手體驗如何把一個 Web App 的程式打包（Dockerize）成 Docker Image 並實際執行成 Container 吧！

### 一、安裝 Docker

要建立 Docker Image 的第一步，當然就是要先在電腦上安裝 Docker 囉。以下都會以 MacOS 的環境作為示範，在 Mac 上可以直接到[官網連結](https://docs.docker.com/docker-for-mac/install)上按照步驟下載。

### 二、準備好打包的目標程式

在安裝好 Docker 後，在接下來我們要來嘗試打包一個 Node.js 的 Web App。相關的程式碼可以在 [docker-demo-app](<https://github.com/HcwXd/docker-tutorial/tree/master/docker-demo-app>) 這個 Github Repo 上找到。

直接 `git clone https://github.com/HcwXd/docker-tutorial.git` 後 `cd docker-demo-app`，我們可以看到資料夾中有五個檔案：

```
.
├── .dockerignore
├── Dockerfile
├── docker.html
├── index.js
└── package.json
```

其中下面的三個檔案就是我們今天要打包的 Web App，簡單來說，這個程式的邏輯就是會建立一個 Server 監聽在 3000 port，收到 request 進來後會渲染 `docker.html` 這個檔案，這時網頁上就會出現一隻可愛的小鯨魚。

### 三、撰寫 Dockerfile

準備好目標程式後就可以來開始打包了，打包的第一步就是要撰寫 Dockerfile。回顧上面的段落所提到的，Dockerfile 透過撰寫命令行告訴 Docker 應該要如何打包我的程式。以資料夾中的 `Dockerfile` 為例：

```dockerfile
FROM node:10.15.3-alpine
WORKDIR /app
ADD . /app
RUN npm install
EXPOSE 3000
CMD node index.js
```

- `FROM node:10.15.3-alpine`
  這行會載入 Node.js 需要的執行環境，每個不同的程式需要的環境可能都不同，這裏下載的是 node:10.15.3-alpine，詳細的其他版本可以在 [Dockerhub](<https://hub.docker.com/_/node/>) 上看到。

- `WORKDIR /app`

  在這個 Docker 的環境之中建立一個工作目錄 `/app`

- `ADD . /app`

  把跟 Dockerfile 同個資料夾的城市加到剛建立的工作目錄 `/app` 中

- `RUN npm install`

  運行 `npm install`，讓 npm 透過讀取 `package.json` 下載相依的 package

- `EXPOSE 3000`

  指定 container 對外開放的 port

- `CMD node index.js`

  我們透過 `node index.js` 來執行我們的 Server

在寫完 Dockerfile 後，如果你有在本機端執行 `npm install` 去產生 `node_modules` 的話，記得加一個 `.dockerignore` 檔案並指定去忽略 `node_modules`。

### 四、打包程式

終於把所有預備檔案準備好後，我們可以在資料夾內透過指令 `docker build`

```
docker build . -t docker-demo-app
```

去建立 Docker Image 並為這個 Image 加上 tag `docker-demo-app`。

然後我們可以再透過指令

```
docker images
```

列出我們全部的 Docker Image 如下

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
docker-demo-app     latest              733776b1db0a        8 minutes ago       74MB
```

現在我們就打包好了我們的第一個 Docker Image 囉！

### 五、實際執行成 Container

有了 Docker Image 後，我們的下一步就可以來實際執行看看。透過上面的 `docker images` 指令，我們可以找到我們建立 Image 的 ID，在這邊是 `733776b1db0a`。

所以我們輸入指令

```
docker run -p 3000:3000 -it 733776b1db0a
```

透過 `docker run`，我們實際把 Image 執行成 Container 了！這時我們看到 terminal 顯示 `listening on port 3000` 後，用瀏覽器打開 [localhost:3000](http://localhost:3000)，就可以迎接一隻 Docker 鯨魚。

![Docker-demo-screenshot](https://github.com/HcwXd/docker-tutorial/blob/master/src/Docker-demo-screenshot.png?raw=true)

接下來要如何關掉 Docker Container 呢？如果發現送 `ctrl + c` 的 signal 進去 Docker Container 沒有反應的話，我們可以開啟另外一個 terminal，然後透過指令 `docker ps` 找到運行中的 Container ID，然後在輸入 `docker stop <ContainerID>`，就可以優雅地關掉我們剛執行的  Docker Container。



## Docker 實例

在成功建立了一個 Docker Image 後，我們要了解 Docker 在現實生活中是如何被運用的。在了解實例時，最重要的概念就是要先知道 Docker 的映像檔堆疊概念。

### 映像檔堆疊

雖然理論上一個映像檔裡可以放多個程式與服務，但 Docker 團隊建議，一個映像檔裡面只裝一個程式，再把映像檔一層一層疊起來以提供一個完整服務。

再次運用蛋糕的比喻，假設我們今天要做一個 Docker 吉祥物形狀的蛋糕，首先我們要先用專門做蛋糕底座的模具烤出蛋糕底座，然後在這個出爐的基底上，再用鯨魚模具烤出鯨魚形狀的蛋糕。現在我們有了一個新鮮的蛋糕有堅固的底座與一隻鯨魚躺在上面，最後我們在這個蛋糕上面放上一個貨櫃模具，烤出 Docker 蛋糕。

還記得我們在上面提到，映像檔是唯讀的，所以我們每次要在映像檔上面再疊一層時，都需要先將他建立成容器實體（烤成蛋糕本人）。然後運用容器再啟動時會在最上面建立可寫層的特性，我們就可以在容器裡在透過另外一個映像檔建立容器實體，最後把著個容器疊上容器的碗糕轉換成映像檔（Dockerize），我們就完成了映像檔堆疊。

### 用 Docker 堆疊出一個網頁環境

現在我們要做出一個 Docker 映像檔，讓我們執行網頁與其後端伺服器，假設我們需要 Alpine OS、Apache Server、MySQL Database、我們要如何把他們疊起來呢？

首先我們要先釐清一個概念，為何 Docker 裡面還需要一個作業系統？事實上這個作業系統稱為 Base OS。我們可以把它想像成一個超級精簡版的作業系統（像 Apline 只要 5 MB），因為容器事實上是使用 Host OS 上的 Kernel，所以這個 Base OS 就只要裝最必要的一些執行檔，如：安裝網路套件的工具等。

疊疊樂的第一步就是從 DockerHub 上下載 Alpine 映像檔，用 Docker 執行映像檔產生容器後，在產生好的容器內在安裝 Apache，等待安裝完成後把整個容器打包成另一個新的映像檔。

而第二層繼續反覆這樣的程序，從 Alpine-Apache 建立的映像檔透過 Docker 產生新的容器，在容器裡安裝下一層 MySQL，然後把整個容器打包，我們就得到一個含有 Alpine、Apache、MySQL 的映像檔了！



## 延伸閱讀

暸解 Docker 的概念與基本教學後，接下來可以透過 [Docker —— 從入門到實踐](<https://philipzheng.gitbooks.io/docker_practice/content/>)，去進一步了解 Docker 的用法囉！

