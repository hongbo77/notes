# 题目描述
求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出1~n区间中1出现的次数。

# 解法一  
暴力法，直接求1到n的整数中1出现的次数：  

    public int NumberOf1Between1AndN_Solution(int n) {
        int cnt = 0;
        for(int i=1; i<=n; i++){
            while(i!=0){
                if(i%10==1)
                    cnt++;
                i/=10;
            }
        }
        return cnt;
    }  

这种解法复杂度太大，没能AC通过。

# 解法二  自己的解法
由于书上没看懂，所以自己写了一个解法
由于暴力法中，有很多重复求解的过程,因此考虑减少重复求解过程；  
求小于10^1次方的数字1~9中1出现的次数，并存到数组act[1]中；
求小于10^2的数1~99中1出现的次数，并存到act[2]中；此步可以利用act[1]的结果；
求小于10^i次方的数中1出现的次数，可以利用到10^(i-1)中1出现的次数act[i-1]
act[0] = 0;      此位无实际意义，但会起作用
act[1] = 1;       1~9中1出现的次数
act[2] = 10 * 1 + 10 = 20;   小于100的数(1~99)中1出现的次数
规律就是：act[i] = 10*act[i-1] + 10^(i-1);   小于10^i次方的整数中1出现的次数;需要加10^(i-1)次方是因为比如100~199中，开头的1出现了100次；
比如小于10的正整数中1出现的次数为1；
小于100的正整数中1出现的次数为10*1+10=20；
小于1000的正整数中1出现的次数为10*20+100=300；
小于10000的正整数中1出现的次数为10*300+1000=4000；

对于某个数n,要求1~n中1出现的次数，比如1~21345中1出现的次数；  
1.求21340~21345中,最后一位导致的1出现的次数
2.求21300~21339中，最后2位导致的1出现的次数
3.求21000到21299中，最后3位导致的1出现的次数
4.求20000到20999中，最后4位导致的1出现的次数（由于千分位是1，见下面“特别注意一点”）
5.求1~19999中，最后5位导致的1出现的次数
也可以反过来从第5步到第1步来理解，更直观，但是需要先求出数一共有多少位，比较麻烦。
特别注意一点：求最后k位中1出现的次数，如果k为1,那么出现的次数为act[k-1] + k%(10^(k-1)+1)，k%(10^(k-1)+1代表第k位为1的数字个数

    //测试用例 1， 2， 10， 11， 19， 20， 99， 100， 10000 .etc;
    public class Solution {
        public int NumberOf1Between1AndN_Solution(int n) {
            int[] act = new int[8]; act[0] = 0;
            for(int i=1; i<8; i++) act[i] = 10 * act[i-1] + (int)Math.pow(10, i-1);
            int remain=0;
            int mi_add_1 = 1;
            int cnt = 0;
            int last;
            while(n!=0){
                last = n % 10;
                if(last > 1){
                    cnt += last * act[mi_add_1 - 1] + (int)Math.pow(10, mi_add_1 - 1);
                }else if(last == 1){
                    cnt += act[mi_add_1 - 1] + remain + 1;
                }
                remain += last * Math.pow(10, mi_add_1-1);
                mi_add_1 ++;
                n /= 10;
            }
            return cnt;
        }
    }  


