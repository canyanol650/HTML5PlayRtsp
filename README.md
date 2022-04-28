# HTML5PlayRtsp

### 使用方法
1. 发布AnyExeToService项目.
2. 发布VideoAdapter项目.
3. 将VideoAdapter项目发布的文件拷贝到AnyExeToService项目的发布文件里
4. 执行AnyExeToService.exe


### 所用到的开源项目

#### AnyExeToService 
https://github.com/cxwl3sxl/AnyExeToService

#### AServer 
https://github.com/kklldog/AServer

### 技术原理
1. 用户安装浏览器插件VideoAdapter,安装完成之后会在客户机器监听8536端口.
1. 当用户想播放某个RTSP视频的时候.发送一个跨域请求到客户机器的8536端口,参数是某个rtsp播放地址
2. 收到播放地址之后.调用Process 执行rtsp转flv命令.
3. 返回flv播放地址


### RTSP转flv原理

nginx-rtmp 模块


### AnyExeToService
将VideoAdapter项目的部署文件.exe 转换成windows 服务


```
        let input = {};
        input["url"] = url;
        
        that._videoConvertService.convertRtsp(input, function (r) {
         if (r.success) {
            //r.result
         }
         else
         {
         // ("视频获取失败");
         }
        });
        
```


```
  /**
   * 请求
   * @param options
   */
  doAjax(options) {
    if (options.type == null) {
      options.type = "POST";
    }

    if (options.url == null) {
      options.url = "";
    }

    if (options.timeout == null) {
      options.timeout = 5000;
    }

    if (options.onComplate == null) {
      options.onComplate = function () {
      }
    }

    if (options.onError == null) {
      options.onError = function () {

      }
    }

    if (options.onSuccess == null) {
      options.onSuccess = function () {
      }
    }

    // if (options.data) {
    //   options.data = "";
    // }

    var xml;
    var ActiveXObject: (type: string) => void;
    if (typeof ActiveXObject != 'undefined') {
      var aVersions = ["Microsoft.XMLHTTP", "Msxml2.XMLHttp.6.0", "Msxml2.XMLHttp.5.0", "Msxml2.XMLHttp.4.0", "Msxml2.XMLHttp.3.0"];
      for (var i = 0; i < aVersions.length; i++) {
        try {
          xml = new ActiveXObject(aVersions[i]);
        } catch (e) { }
      }
    } else if (typeof XMLHttpRequest != 'undefined') {
      xml = new XMLHttpRequest();
    }

    xml.open(options.type, options.url, true);

    var timeoutLength = options.timeout;

    var requestDone = false;

    setTimeout(function () {
      requestDone = true;
    }, timeoutLength);

    xml.onreadystatechange = function () {

      if (xml.readyState == 4 && !requestDone) {

        if (httpSuccess(xml)) {

          options.onSuccess(httpData(xml));
        }

        else {
          options.onError();
        }

        options.onComplate();

        xml = null;
      }
    };

    xml.send(JSON.stringify(options.data));

    function httpSuccess(r) {
      try {
        return !r.status && location.protocol == "file:"
          ||
          (r.status >= 200 && r.status <= 300)
          ||
          r.status == 304
          ||

          navigator.userAgent.indexOf("Safari") >= 0
          && typeof r.status == "undefined";
      } catch (e) {
      }
      return false;
    }

    function httpData(r) {
      var ct = r.getResponseHeader["responseType"];
      if (ct) {
        if (ct == "script") {
          var data = eval.call(window, data);
        }
        if (ct == "xml") {
          return r.responseXML;
        }
        if (ct == "json") {
          return JSON.parse(r.responseText);
        }
      }
      return r.responseText;
    }
  }

  convertRtsp(input, callback) {
    function onSuccess(data) {

      var result = JSON.parse(data)
      //console.log(data);
      if (result.code == -1) {
        abp.notify.error(result.retmsg);
        return callback()
      }
      return callback(result);
    }
    function onError() {
      callback({ success: false });

    }

    let url = "http://localhost:8536/api/Convert";
    var options = new Object();
    options["type"] = "POST";
    options["url"] = url;
    // options["url"] = url + "?waitTime=3" + "&" + "t=" + Math.random();	//URL后面加一个随机参数的目的是为了防止IE内核浏览器的数据缓存
    options["data"]=input;
    options["timeout"] = 5000;
    options["onSuccess"] = onSuccess;
    options["onError"] = onError;
    this.doAjax(options);
  }
```
