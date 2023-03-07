📃 开源协议 Apache License Version 2.0 see http://www.apache.org/licenses/LICENSE-2.0.html
# 简介

基于redis实现的的限流器


版本说明
--------------------------------------------------------------------------

PHP7.0+版本 必须安装phpredis拓展

安装教程
--------------------------------------------------------------------------

composer require v10086/redis-ratelimit:v1.0

使用示例
--------------------------------------------------------------------------

```php
namespace v10086;
//访问频率限制  使用示例:
// \library\RedisRateLimit::$redisHandler=已完成连接接的redis C 端;
// \v10086\RedisRateLimit::check('api',[5=>2,10=>5]); 
// 表示某个api 5秒内最多访问2次 ,10秒内最多访问5次 
class RedisRateLimit {
    
    public static $redisHandler=null; //redis操作句柄 默认为空
    public static $msg='ok';

    public static function check($limitKey,$policy){
        
        $rateKeyPrefix='rateLimit:'.$limitKey.':';
        foreach ($policy as $policyKey=>$policyVal) {
            $rateKey=$rateKeyPrefix.$policyKey;
            $count= self::$redisHandler->INCR($rateKey,1);
            if($count==1){
               self::$redisHandler->EXPIRE($rateKey,$policyKey);
            }
            if ($count>$policyVal) {
                self::$msg=[$policyKey=>$policyVal];
                return false;
            }
        }
        return  true;
    }
}
```
