<?php
/**
  * wechat php test
  */

//define your token
define("TOKEN", "weixin");
$wechatObj = new wechatCallbackapiTest();
//$wechatObj->valid();

if(isset($_GET["echostr"])){
    $wechatObj->valid();
}else{
    $wechatObj->responseMsg();
}

class wechatCallbackapiTest
{
    public function valid()
    {
        $echoStr = $_GET["echostr"];

        //valid signature , option
        if($this->checkSignature()){
            header('content-type:text');
            echo $echoStr;
            exit;
        }
    }

    public function responseMsg()
    {
        //get post data, May be due to the different environments
        $postStr = $GLOBALS["HTTP_RAW_POST_DATA"];

        //extract post data
        if (!empty($postStr)){
                /* libxml_disable_entity_loader is to prevent XML eXternal Entity Injection,
                   the best way is to check the validity of xml by yourself */
                libxml_disable_entity_loader(true);
                //拿回来的全部信息
                $postObj = simplexml_load_string($postStr, 'SimpleXMLElement', LIBXML_NOCDATA);
                $fromUsername = $postObj->FromUserName;
                $toUsername = $postObj->ToUserName;
                $keyword = trim($postObj->Content);
                //$mediaId = $postObj->MediaId;
                $time = time();
                $textTpl = "<xml>
                            <ToUserName><![CDATA[%s]]></ToUserName>
                            <FromUserName><![CDATA[%s]]></FromUserName>
                            <CreateTime>%s</CreateTime>
                            <MsgType><![CDATA[%s]]></MsgType>
                            <Content><![CDATA[%s]]></Content>
                            <FuncFlag>0</FuncFlag>
                            </xml>";
                $newsTpl = '<xml>
                            <ToUserName><![CDATA[%s]]></ToUserName>
                            <FromUserName><![CDATA[%s]]></FromUserName>
                            <CreateTime>%s</CreateTime>
                            <MsgType><![CDATA[%s]]></MsgType>
                            <Image>
                            <MediaId><![CDATA[tTzBy94x_sL8oLGLllwvbwaHwaBj_F33wnTzOMJloNoFY2MxQT90l3VISOrw3P0A]]></MediaId>
                            </Image>
                            </xml>';
            	$voiceTpl = "<xml>
                                <ToUserName><![CDATA[%s]]></ToUserName>
                                <FromUserName><![CDATA[%s]]></FromUserName>
                                <CreateTime>%s</CreateTime>
                                <MsgType><![CDATA[%s]]></MsgType>
                                <Voice>
                                <MediaId><![CDATA[-HywPUXVqEcy1nnx_1YQqfQbcK7Sp74ne6llFeWJ5ztQrX-p_91jZkh9E96l8Q_z]]></MediaId>
                                </Voice>
                            </xml>";
            	$videoTpl = "<xml>
                            <ToUserName><![CDATA[%s]]></ToUserName>
                            <FromUserName><![CDATA[%s]]></FromUserName>
                            <CreateTime>%s</CreateTime>
                            <MsgType><![CDATA[%s]]></MsgType>
                            <Video>
                            <MediaId><![CDATA[ZH3moNEGdDe5aWvpHMMrgEsN4eENvFjTCXCyOxHsxnrmPTfYIrStTsZJuaK3PVj4]]></MediaId>
                            <Title><![CDATA[%s]]></Title>
                            <Description><![CDATA[%s]]></Description>
                            </Video>
                            </xml>";

                //$keyword是接受的信息
                if(!empty( $keyword ))
                {

                    $msgType = "text";
                    // $contentStr = "Welcome to wechat world!";

                    if($keyword=='肖景允'){
                        //$contentStr返回内容；

                        $contentStr = '雷州的大石狗';
                        $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                        echo $resultStr;

                    }else if($keyword=='图片'){
                        $msgType = 'image';
                        //$contentStr = $mediaId;
                        $resultStr = sprintf($newsTpl, $fromUsername, $toUsername, $time, $msgType);
                        echo $resultStr;
                    }else if($keyword=='语音'){
                        $msgType = 'voice';
                        //$contentStr = $mediaId;
                        $resultStr = sprintf($voiceTpl, $fromUsername, $toUsername, $time, $msgType);
                        echo $resultStr;
                    }else if($keyword=='视频'){
                        $msgType = 'video';
                        //$contentStr = $mediaId;
                        $resultStr = sprintf($videoTpl, $fromUsername, $toUsername, $time, $msgType);
                        echo $resultStr;
                    }else if($keyword=='李有文'){
                        $contentStr = '李有文，副教授，1967年2月生。1998年毕业于北京理工大学应用数学专业，获理学硕士学位，研究方向为最优化理论与方法';
                        $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                        echo $resultStr;
                    }else if($keyword=='郑汝红'){
                        $contentStr = '韩国三级片女星';
                        $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                        echo $resultStr;
                    }else if($keyword=='黄铭基'){
                        $contentStr = '前端工程师';
                        $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                        echo $resultStr;
                    }else{
                        //header('Access-Control-Allow-Origin:*');
                         $apiKey = "45e0f62bf6774ee39633a14a61e6e475";
                         $apiUrl = "http://www.tuling123.com/openapi/api?key=KEY&info=INFO";
                         header("Content-type: text/html; charset=utf-8");
                         $reqInfo = $keyword;
                         $url = str_replace("INFO",$reqInfo,str_replace("KEY",$apiKey,$apiUrl));
                         $res = file_get_contents($url);

                         $contentStr = json_decode($res)->text;
                         $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                         echo $resultStr;
                        //$contentStr = json_encode($postObj);
                        //$resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                        //echo $resultStr;
                    }

                }else{
                    //接受图片获取图片mediaId;
                     $msgType = 'text';
                     $contentStr = json_encode($postObj);
                     $resultStr = sprintf($textTpl, $fromUsername, $toUsername, $time, $msgType, $contentStr);
                     echo $resultStr;
                    echo "Input something...";
                }

        }else {
            echo "";
            exit;
        }
    }
    private function checkSignature()
    {
        // you must define TOKEN by yourself
        if (!defined("TOKEN")) {
            throw new Exception('TOKEN is not defined!');
        }

        $signature = $_GET["signature"];
        $timestamp = $_GET["timestamp"];
        $nonce = $_GET["nonce"];

        $token = TOKEN;
        $tmpArr = array($token, $timestamp, $nonce);
        // use SORT_STRING rule
        sort($tmpArr, SORT_STRING);
        $tmpStr = implode( $tmpArr );
        $tmpStr = sha1( $tmpStr );

        if( $tmpStr == $signature ){
            return true;
        }else{
            return false;
        }
    }
}
