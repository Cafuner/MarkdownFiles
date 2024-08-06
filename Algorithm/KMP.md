```cpp
//KMP算法

//预处理模式串的next数组
//next[j]表示对于子串str[0:j],前next[j]+1个字符和最后next[j]+1个字符相同
void getNxt(string& str, int* nxt)
{
    int len=str.length();
    nxt[0] = -1;
    for(int i=1, j=-1; i<len; i++)
    {
        while(j>-1 && str[j+1]!=str[i])
        {
            j = nxt[j];
        }
        if(str[j+1]==str[i])
        {
            j++;
            nxt[i]=j;
        }
        else
        {
            nxt[i] = -1;
        }
    }
}

//haystack is the text string, needle is the pattern string
//return the first index where the pattern string appears in the text string
int KMP(string haystack, string needle)
{
	int lena=haystack.length();
    int lenb=needle.length();
    if(lenb==0)
        return 0;
    int* nxt = new int[lenb];
    getNxt(needle, nxt);
    for(int i=0, j=-1; i<lena; i++) // j表示当前模式串pattern[j]与主串s[i]相等
    {
        while(j>-1 && needle[j+1]!=haystack[i])
        {
            j = nxt[j];
        }
        if(needle[j+1]==haystack[i])
        {
            j++;
        }
        if(j==lenb-1)
        {
            return i-j;
        }
    }
    return -1;

}
```

