```c
void qsort(void *const base,size_t const num,size_t const width,int(* const cmp)(void const *,void const *) ){
    
    // 一个保存已处理了的子数组的栈
    char *lstack[STKSIZ];
    char *hstack[STKSIZ];
    int stkptr =0;

    if(num<2)
        return ;

    //两个指针分别指向数组起始    
    char *l=(char *)base;
    char *h=(char *)base+width*(num-1);

//goto 语句起始位置 递归开始部分(栈实现递归)
recurse:

    size_t const size=(h-l)/width +1;
    //小于8个元素 使用选择排序(在函数运行时 最终肯定会有某一部分的元素小于8个 这时执行选择排序)
    if(size<=CUTOFF)
        short_sort(l,h,width,cmp);
    else{ 
        //首先选择一个分区元素。算法的效率要求我们找到一个近似于值的中值，而且我们要快速地选择一个。
        //我们选择第一个、中间和最后一个元素的中位数，以避免在已经排序的数据或由多个排序的运行附加
        //在一起组成的数据面前出现糟糕的性能。
        //测试表明，对于后一种情况，三种算法的中位数比简单地选择中间元素提供更好的性能。
        
        // 中间元素(防止两数相加溢出):
        char *mid = lo + (size / 2) * width;

        //解释一下 对三个数排序 比较三次 最后mid就是中位数 
        // Sort the first, middle, last elements into order:
        if (cmp( lo, mid) > 0)
            swap(l, mid, width);

        if (cmp( lo, hi) > 0)
            swap(l, h, width);

        if (cmp( mid, hi) > 0)
            swap(mid, h, width);
        
        //现在把数组分成三个部分 1.< 2.== 3.>
        
        char *loguy = l;
        char *higuy = h;

        //进行一次排序 以mid为中心分两拨(实际处理中mid变化)
        for(;;){
            if(mid>loguy){
                //此处很精妙 前面已经把low mid high 进行一次排序 
                //所以起始的时候loguy一定小于mid 直接执行 do里面的语句
                do{
                    loguy+=width;                    
                }while(loguy<mid&&cmp(loguy,mid)<=0);
            }
            if(mid<=loguy){
                do{
                    loguy+=width;                    
                }while(loguy<=hi&&cmp(loguy,mid)<=0);           
            }
            do{
                higuy-=width;
            }while(higuy>mid&&cmp(higuy,mid)>0);

            if(higuy<loguy)
                break;
            //交换双指针元素    
            swap(loguy,higuy,width);

            //右边的元素全部大于mid    
            if(mid==higuy)
                mid=loguy;
        }
        
    }
}
```
