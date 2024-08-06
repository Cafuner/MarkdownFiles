# Segment Tree 线段树



```c++
typedef long long int ll;

class SegmentTree
{
private:
    int n;
    int size;
    ll* nodes;
    ll* lazy; //区间修改延迟标记
    ll INIT_VALUE;

    ll query(int from, int to, int k, int left, int right)
    {
        if(to<left || from>right) //不相交
            return INIT_VALUE;
        else if(from<=left && right<=to) //全包含
            return nodes[k];
        else
        {
            pushdown(k, left, right);
            ll leftRes = query(from, to, k*2+1, left, (left+right)/2);
            ll rightRes = query(from, to, k*2+2, (left+right)/2+1, right);
            return merge(leftRes, rightRes);
        }
    }

    void update(int from, int to, int operand, int k, int left, int right)
    {
        if(to<left || from>right) //不相交
            return;
        else if(from<=left && right<=to) //全包含
        {
            lazy[k] = rangeCumulate(lazy[k], operand);
            nodes[k] = rangeUpdate(left, right, nodes[k], operand);
        }
        else
        {
            pushdown(k, left, right);
            update(from, to, operand, 2*k+1, left, (left+right)/2);
            update(from, to, operand, 2*k+2, (left+right)/2+1, right);
            nodes[k] = merge(nodes[2*k+1], nodes[2*k+2]);
        }
    }

    void pushdown(int k, int left, int right)
    {
        if(lazy[k])
        {
            nodes[2*k+1]= rangeUpdate(left, (left+right)/2, nodes[2*k+1], lazy[k]);
            nodes[2*k+2]= rangeUpdate((left+right)/2+1, right, nodes[2*k+2], lazy[k]);
            lazy[2*k+1] = rangeCumulate(lazy[2*k+1], lazy[k]);
            lazy[2*k+2] = rangeCumulate(lazy[2*k+2], lazy[k]);
            lazy[k] = 0;
        }
    }

    //以求区间和为例
    ll merge(ll leftRes, ll rightRes)
    {
        return leftRes+rightRes;
    }

    //以区间内所有数加operand为例
    ll rangeCumulate(ll old, ll operand)
    {
        return old+operand;
    }

    //以区间内所有数加operand为例
    ll rangeUpdate(int left, int right, ll old, ll operand)
    {
        return old+(right-left+1)*operand;
    }

public:
    SegmentTree(int n, ll* init, int init_value)
    {
        this->n = n;
        this->INIT_VALUE = init_value;
        size = 1;
        while(size<n)
            size = size<<1;
        nodes = new ll[2*size-1];
        if(init!=nullptr)
        {
            ll* p = init;
            for(int i=0; i<n; i++)
            {
                nodes[i+size-1] = *p;
                p++;
            }
            for(int i=n+size-1; i<2*size-2; i++)
            {
                nodes[i] = INIT_VALUE;
            }
        }
        else
        {
            for(int i=0; i<size; i++)
                nodes[i+size-1] = INIT_VALUE;
        }
        for(int i=size-2; i>=0; i--)
        {
            nodes[i] = merge(nodes[i*2+1], nodes[i*2+2]);
        }
        lazy = new ll[2*size-1];
        for(int i=0; i<2*size-1; i++)
            lazy[i] = 0;
    }

    void update(int from, int to, ll operand)
    {
        update(from, to, operand, 0, 0, size-1);
    }

    ll query(int from, int to)
    {
        return query(from, to, 0, 0, size-1);
    }


    ~SegmentTree()
    {
        delete[] nodes;
        delete[] lazy;
    }

    void display()
    {
        for(int i=0; i<2*size-1; i=i*2+1)
        {
            for(int j=i; j<i*2+1; j++)
                printf("%lld ", nodes[j]);
            printf("\n");
        }
    }
};
```

