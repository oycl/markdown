Building a Module
����ģ��
===================================

> Warning
> This tutorial requires having installed Odoo
> ����
> ���̳���Ҫ��װOdoo

Start/Stop the Odoo server ����/ֹͣOdoo����
-----------------------------------
    Odoo uses a client/server architecture in which clients are 
    web browsers accessing the Odoo server via RPC.
    Odoo ʹ�� C/S�ܹ����ͻ��˼�Web�����ͨ��RPCЭ�����Odoo����ˡ�

    Business logic and extension is generally performed on the server side, 
    although supporting client features (e.g. new data representation such 
    as interactive maps) can be added to the client.
    ҵ���߼�����չͨ���ڷ����ִ�У���ֻ������ӿͻ���֧�ֵ��������Ż���
    �ͻ�����Ӵ��루���磬�ڽ���ӳ���������ݵı�ʾ��

    In order to start the server, simply invoke the command odoo-bin 
    in the shell, adding the full path to the file if necessary:
    Ϊ��������������ֻ��Ҫ��shell�е�������odoo-bin�������Ҫ�����������·������
    `odoo-bin`

    The server is stopped by hitting Ctrl-C twice from the terminal, 
    or by killing the corresponding OS process.
    ͨ���������� Ctrl-C �����ɱ����Ӧ��ϵͳ������ֹͣOdoo����





Build an Odoo module ����ģ��
-----------------------------------




Basic views ������ͼ
-----------------------------------




Relations between models ģ�͹���
-----------------------------------



Inheritance �̳�
-----------------------------------




Computed fields and default values �����ֶκ�Ĭ��ֵ
-----------------------------------




Onchange 
-----------------------------------





Model constraints ģ��Լ��
-----------------------------------




Advanced Views �߼���ͼ
-----------------------------------




Workflows ������
-----------------------------------





Security ��ȫ
-----------------------------------





Wizards ��
-----------------------------------



Internationalization ���ʻ�
-----------------------------------





Reporting ����
-----------------------------------



WebServices Web����
-----------------------------------




  
### С����
  С��������html��\<h3\><br />
  С����ĸ�ʽ���� ### С����<br />
  ע��#�ͱ����ַ��м�Ҫ�пո�
### ����ĵȼ���ʾ��
	���ڱ���ĵȼ���ʾ��������Ϊ�����ȼ�����ʾ���ı���С���μ�С����ͬ�ȼ�֮���Ծ���#�ĸ�������ʶ��һ��������һ��#����������������#���Դ����ơ�
		#һ������
		##��������
		###��������
		####�ļ�����
		#####�弶����
		######��������
### ע��!!!���������﷨����ʾ�Ҷ�����С����������!!! 

### �����ı���
    ����һ�����е��ı���,ֻҪ����Tab���������ּ���
        
### �����ı���  
    ����һ���ж��е��ı���
    �����д������,ÿ������ֻҪ��������Tab���������ּ���
    �������������һ�δ���

### �������ǿ����ڶ����ı���������һ�δ���,��һ��Java�汾��HelloWorld��
    public class HelloWorld {

      /**
      * @param args
	    */
	    public static void main(String[] args) {
		    System.out.println("HelloWorld!");

	    }

    }

### ����
1.[���������������ӵ�www.google.com](http://www.google.com)<br />
2.[�����������������ӵ��ҵĲ���](http://guoyunsky.iteye.com)<br />

###ֻ����ʾ�ٶȵ�ͼƬ
![baidu-images](http://www.baidu.com/img/bdlogo.png "baidu")  

###ֻ����ʾͼƬ�������õ������·��
![github-01.jpg](/images/01.jpg "github-01.jpg")

### ��ʾͼƬҲ������ԭ����html��ǩ
<img src="http://su.bdimg.com/static/superplus/img/logo_white.png" />

###����ĳ��ͼƬ����һ����ҳ,����������github��icornȻ���ٽ���www.github.com
[![image]](http://www.github.com/)
[image]: /images/02.jpg "github-02.jpg"

### ���ֱ�Щ�ַ���Χ
> ���ֱ�Щ�ַ���Χ
>
> ֻҪ������ǰ�����>�ո񼴿�
>
> �����Ҫ���еĻ�,����һ��,����>�ո񼴿�,���治������
> ��> ֻ�ܷ������ײ���Ч

### ���ֱ�Щ�ַ���Χ,���ذ�Χ
> ���ֱ�Щ�ַ���Χ��ʼ
>
> > ֻҪ������ǰ�����>�ո񼴿�
>
>  > > �����Ҫ���еĻ�,����һ��,����>�ո񼴿�,���治������
>
> > > > ��> ֻ�ܷ������ײ���Ч

### �������ֵĸ���
�������ʹһ�λ��������ָ�����ʾ������ͻ��ǿ�������ã���ô���԰�����\`\`��Χ������
ע���ⲻ�ǵ����ţ�����Tab��������1����ߵİ�����ע��ʹ��Ӣ�����뷨����<br />
	example��
		Thank`You`. Please `Call` Me `Coder`
### ����Ƭ�θ�����ʾ
GitHub��markdown�﷨��֧�ֲ������ԵĴ���Ƭ�θ�����ʾ��ֻ��Ҫ�ڴ������һ�к���һ����\`\`\`��ǡ�
```Java
	public static void main(String[] args){} //Java
```
```c
	int main(int argc,char *argv[]) //C
```
```javascript
	document.getElementById("myH1").innerHTML="Welcome to my Homepage";//javascript
```
```cpp
	string &operator+(const string& A,const string& B) //cpp
```
	
### list�б���Ŀʹ��
д����ʱ�������õ�list�б���Ŀ��GitHub��markdown�﷨��Ҳ֧��ʹ��Բ������༭��ʱ��ʹ�õ����Ǻ�*��
* �������й�
* ���У�����
* ��ѧ���廪��ѧ 
<br/>ע�⣺�Ǻ�*����Ҫ��һ���ո񡣷�����ʾΪ��ͨ�Ǻš�
GitHub��֧�ֶ༶���list�б���Ŀ��
* �������
	* �ű�����
		* Python

### �����ַ�����
��һЩ�����ַ���<,#��,ֻҪ�������ַ�ǰ�����ת���ַ�\����<br />
���뻻�еĻ���ʵ����ֱ����html��ǩ\<br /\>
    

### ������
��Markdown�в�����Ƚ��鷳����ҪMarkdown����չ�﷨�����ǲ���HTML��û����ô�鷳�ˣ�������ǿ���ͨ�����߾ȹ��ķ�ʽ��������       
��Markdown�У�`&`���ź�`<`���Զ�ת����HTML��

	<div>
	    <table border="0">
		  <tr>
		    <th>one</th>
		    <th>two</th>
		  </tr>
		  <tr>
		    <td>Hello</td>
		    <td>���</td>
		  </tr>
	    </table>
	</div>
	
<div>
        <table border="0">
	  <tr>
	    <th>one</th>
	    <th>two</th>
	  </tr>
	  <tr>
	    <td>Hello</td>
	    <td>���</td>
	  </tr>
	</table>
</div>
