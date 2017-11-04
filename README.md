# Colored-Sticks

Colored Sticks

Description

You are given a bunch of wooden sticks. Each endpoint of each stick is colored with some color. Is it possible to align the sticks in a straight line such that the colors of the endpoints that touch are of the same color?

Input

Input is a sequence of lines, each line contains two words, separated by spaces, giving the colors of the endpoints of one stick. A word is a sequence of lowercase letters no longer than 10 characters. There is no more than 250000 sticks.

Output

If the sticks can be aligned in the desired way, output a single line saying Possible, otherwise output Impossible.

Sample Input

blue red

red violet

cyan blue

blue magenta

magenta cyan

Sample Output

Possible

Hint

Huge input,scanf is recommended.

这道题大意是：已知有n根木棒，每个木棒有两种颜色表示两端，问：能不能将所有的木棒连接成一条直线（连接处颜色相同）。

可以考虑无向图的欧拉通路问题：将每种颜色看成图中的一个节点，木棒看作连接节点的边，于是判断两点：

1，每种颜色（节点）的度

2，是否为连通图

首先，每种颜色的度可以通过每条木棒的两端颜色的累积得到，问题是，每种颜色都是字符串，怎么关联每种颜色和度呢？

最容易想到的是Hash，这肯定是可行的。例如degree [ hash(red) ]=5。表示颜色为红色的度为5。

但是，既然提示用trie树来做，那么trie树的节点的数据域就可以保存每种颜色的id（相当于分配的hash值，可以通过一个全局变量自增产生），这样经过将每种颜色字符串插入到tree中以后，通过trie树的search操作就能高效的获取每种颜色对应的id，没插入一种颜色，为其产生一个id，只需要注意相同颜色插入时的处理即可。
其次，判断无向图是否连通可以使用并查集来判定：经过n-1各union操作后，所有节点都在一个集合（树形结构表示集合的话，即所有节点的父节点（集合代表）都一样）。由于每种颜色是由字符串来标示的，每个集合保存颜色对应的唯一id。

通过上面两个步骤的判定，就可以得出结果。

#include <iostream>   
  
#include <cstring>   
  
using namespace std;   

const int max_size=500001;   

char s1[11],s2[11];   

int p[max_size];  

int r[max_size];  

int degree[max_size];   

int num=1;   

struct TreeNode  

{   

        int id;//the id of the string   
        
        TreeNode * next[27];  
        
        TreeNode ()   
        
        {   
        
                id=0;   
                
                for(int i=0;i<27;i++)   
                
                        next[i]=NULL;   
                        
        }   
        
};   

TreeNode * root;   

void insert(char *s ,TreeNode * root)   

{   

        TreeNode *p = root; 
        
        int i = 0;   
        
        int l = strlen(s);  
        
        for(i=0;i<l;i++)  
        
        {   
        
                if(p->next[s[i]-'a']==NULL)  
                
                        p->next[s[i]-'a']=new TreeNode;   
                        
                p=p->next[s[i]-'a'];   
                
        }   
        
        if(p->id==0)//first insert 
        
            p->id = num++;   
            
}   

int search(char *s,TreeNode *root)  

{   

        TreeNode * p = root;  
        
        if(p==NULL)   
        
                return -1; 
                
        int i=0;   
        
        int l = strlen(s);
        
        for(i=0;i<l;i++)  
        
        {   
        
            if(p->next[s[i]-'a']==NULL)   
            
                    return -1;   
                    
            else  
            
                p=p->next[s[i]-'a'];   
        }   
        
        return p->id;   
        
}   

void make_set(int x)   

{   

        p[x]=x; 
        
        r[x]=1; 
        
}   

int find_set(int x)  

{   

    if(p[x]!=x)   
    
            p[x]=find_set(p[x]); 
            
    return p[x];  
    
}   

void union_set(int x,int y)   

{   

        if(r[x]>r[y])   
        
                p[y]=x;  
                
        else if(r[x]<r[y])  
        
                p[x]=y;   
                
        else  
        
        {   
        
                r[y]++;  
                
                p[x]=y;  
                
        }   
        
}   

int main()   

{   

        root = new TreeNode;
        
        memset(p,0,sizeof(p));   
        
        memset(degree,0,sizeof(degree));   
        
        while(scanf("%s %s",s1,s2)!=EOF)  
        
        {   
        
                insert(s1,root);
                
                insert(s2,root);   
                
                int id1 = search(s1,root);  
                
                int id2 = search(s2,root); 
                
                degree[id1]++;   
                
                degree[id2]++;   
                
                
                if(p[id1]==0)   
                
                        make_set(id1);   
                        
                if(p[id2]==0)   
                
                        make_set(id2);   
                        
                union_set(find_set(id1),find_set(id2));   
                
        }   
        
        int i = 0;  
        
        int sum=0;   
        
        //if the num of nodes whose degree is odd are more than 2.   
        
        for(i=1;i<num;i++)   
        
        {   
        
            if(degree[i]%2!=0)sum++;   
            
            if(sum>2)       
            
            {   
            
                    cout<<"Impossible"<<endl;  
                    
                    return 0;   
                    
            }   
            
        }   
        
        //if the g is joint.   
        
        for(i=1;i<num;i++)   
        
                if(find_set(i) != find_set(1))   
                
                {   
                
                    cout<<"Impossible"<<endl;
                    
                    return 0 ;   
                    
                }   
                
                cout<<"Possible"<<endl;   
                
}   
