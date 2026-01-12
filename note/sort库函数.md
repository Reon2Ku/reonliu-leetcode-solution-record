# sort函数

sort函数自行制定比较规则：

~~~
bool compare(int a,int b)
{
	return a>b;
}
//比较函数中会传入两个参数a和b，return的返回值为true时a在b前，返回值为false时b在a前
sort(vec.begin(),vec.end(),compare);

上述写法实际使用时经常遇到问题，我更推荐使用lamda表达式来声明比较函数

sort(vec.begin(),vec.end(),[](const int& a, const int& b){
return a<b;
});
~~~



