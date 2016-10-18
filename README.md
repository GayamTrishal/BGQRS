# BGQRS
# Question

You are given an array A consisting of N integers. You have to answer M queries on it. Each query belongs to one of the following three types:

    1 L R X : multiply each number in the range AL, AL + 1, ..., AR by X.
    2 L R Y : Replace the elements AL, AL + 1, ..., AR by Y, 2 * Y, ... (R - L + 1) * Y. In other words, the number Ai will be equal to (i - L + 1) * Y for each i from L to R.
    3 L R : Find the product of all numbers in the range AL, AL + 1, ..., AR. As this number could be very large, you have to just find the number of trailing zeros of this number when represented in decimal notation.

Input

The first line of the input contains an integer T denoting the number of test cases. T test cases follow.

The first line of each test case contains two space-separated integers N and M.

The second line contains N space-separated integers denoting A1, A2, ..., AN

For next M lines, each line contains a query.

Each query is given by three or four (please refer to the statement) space separated integers.

The first integer denotes type of the query. For every type of query next two integers denote L and R. For each query of type 1, next integer denote X. For each query of type 2, next integer denote Y.
Output

For each test case, output a single line containing the sum of answers of all the queries of type 3.
Constraints

    1 ≤ T ≤ 5
    1 ≤ N, M, N + M ≤ 105
    1 ≤ L ≤ R ≤ N
    1 ≤ X, Y, Ai ≤ 109

Subtasks

    Subtask #1 (18 points): 1 ≤ N, M, X, Y, Ai ≤ 10
    Subtask #2 (31 points): 1 ≤ N, M ≤ 1000
    Subtask #3 (51 points): original constraints

Example

Input:

    1
    5 5
    2 4 3 5 5
    3 2 4
    3 2 5
    2 2 4 1
    1 3 3 10
    3 1 5

Output:

    5

Explanation

    Array: [2, 4, 3, 5, 5]

    1st query: [4, 3, 5], 4 * 3 * 5 = 60 : answer 1.
    2nd query: [4, 3, 5, 5], 4 * 3 * 5 * 5 = 300 : answer 2.
    3rd query: [2, 4, 3, 5, 5] => [2, 1, 2, 3, 5].
    4th query: [2, 1, 2, 3, 5] => [2, 1, 20, 3, 5].
    5th query: [2, 1, 20, 3, 5], 2 * 1 * 20 * 3 * 5 = 600 - answer 2.

Sum of all answers = 5.

*******************************************************************************************************************************

# Solution

    #include <iostream>
 
    using namespace std;
 
    long long int t,n,m,a[100001][2],A,y[300001][2],up[300001][5],count2,count5,countz,l,r,p,i;
    long long int countx,county,b[100001][2],c[100001][2];
 
    void initialize_up(long long int node)
    {
        long long int i;
        for(i=0;i<5;i++)
        up[node][i]=0;
    }
    void upx(long long int two,long long int five,long long int node)
    {
        up[(2*node)][0]+=two;
        up[(2*node)][1]+=five;
        if(up[(2*node)][4]==0)
            up[(2*node)][4]=1;
        up[(2*node)+1][0]+=two;
        up[(2*node)+1][1]+=five;
        if(up[(2*node)+1][4]==0)
            up[(2*node)+1][4]=1;
    }
    void upy(long long int two,long long int five,long long int node,long long int lo,long long int ro)
    {
        up[(2*node)][0]=two;
        up[(2*node)][1]=five;
        up[(2*node)][2]=lo;
        up[(2*node)][3]=ro;
        up[(2*node)][4]=2;
        up[(2*node)+1][0]=two;
        up[(2*node)+1][1]=five;
        up[(2*node)+1][2]=lo;
        up[(2*node)+1][3]=ro;
        up[(2*node)+1][4]=2;
    }
    void update(long long int node,long long int start,long long int end)
    {
        if(up[node][4]==1)
        {
            y[node][0]+=((end-start+1)*up[node][0]);
            y[node][1]+=((end-start+1)*up[node][1]);
            if(start<end)
                upx(up[node][0],up[node][1],node);
            initialize_up(node);
        }
        else if(up[node][4]==2)
        {
            y[node][0]=((end-start+1)*up[node][0]);
            y[node][1]=((end-start+1)*up[node][1]);
            y[node][0]+=(c[end-up[node][2]+1][0]-c[start-up[node][2]][0]);
            y[node][1]+=(c[end-up[node][2]+1][1]-c[start-up[node][2]][1]);
            if(start<end)
                upy(up[node][0],up[node][1],node,up[node][2],up[node][3]);
            initialize_up(node);
        }
    }
    void updatex(long long int node,long long int start,long long int end)
    {
        if(up[node][4]!=0)
            update(node,start,end);
        if((l<=start)&&(end<=r))
        {
            y[node][0]+=((end-start+1)*count2);
            y[node][1]+=((end-start+1)*count5);
            if(start<end)
                upx(count2,count5,node);
        }
        else if(start<end)
        {
            long long int mid=((start+end)/2);
            if((start>r)||(mid<l))
            {
                if(up[(2*node)][4]!=0)
                    update((2*node),start,mid);
            }
            else
                updatex((2*node),start,mid);
            if((mid+1>r)||(end<l))
            {
                if(up[(2*node)+1][4]!=0)
                    update((2*node)+1,mid+1,end);
            }
            else
                updatex((2*node)+1,mid+1,end);
            y[node][0]=y[(2*node)][0]+y[(2*node)+1][0];
            y[node][1]=y[(2*node)][1]+y[(2*node)+1][1];
        }
    }

    void updatey(long long int node,long long int start,long long int end)
    {
        if(up[node][4]!=0)
            update(node,start,end);
        if((l<=start)&&(end<=r))
        {
            y[node][0]=((end-start+1)*count2);
            y[node][1]=((end-start+1)*count5);
            y[node][0]+=(c[end-l+1][0]-c[start-l][0]);
            y[node][1]+=(c[end-l+1][1]-c[start-l][1]);
            if(start<end)
                upy(count2,count5,node,l,r);
        }
        else if(start<end)
        {
            long long int mid=((start+end)/2);
            if((start>r)||(mid<l))
            {
                if(up[(2*node)][4]!=0)
                    update((2*node),start,mid);
            }
            else
                updatey((2*node),start,mid);
            if((mid+1>r)||(end<l))
            {
                if(up[(2*node)+1][4]!=0)
                    update((2*node)+1,mid+1,end);
            }
            else
                updatey((2*node)+1,mid+1,end);
            y[node][0]=y[(2*node)][0]+y[(2*node)+1][0];
            y[node][1]=y[(2*node)][1]+y[(2*node)+1][1];
        }
    }

    void value(long long int node,long long int start,long long int end)
    {
        if(up[node][4]!=0)
            update(node,start,end);
        if((l<=start)&&(r>=end))
        {
            countx+=y[node][0];
            county+=y[node][1];
        }
        else if(start<end)
        {
            long long int mid=((start+end)/2);
            if((start>r)||(mid<l))
            {
                if(up[(2*node)][4]!=0)
                    update((2*node),start,mid);
            }
            else
                value((2*node),start,mid);
            if((mid+1>r)||(end<l))
            {
                if(up[(2*node)+1][4]!=0)
                    update((2*node)+1,mid+1,end);
            }
            else
                value((2*node)+1,mid+1,end);
            y[node][0]=y[(2*node)][0]+y[(2*node)+1][0];
            y[node][1]=y[(2*node)][1]+y[(2*node)+1][1];
        }
    }

    void build(long long int node,long long int start,long long int end)
    {
        if(start==end)
        {
            y[node][0]=a[start][0];
            y[node][1]=a[start][1];
            initialize_up(node);
        }
        else
        {
            long long int mid=((start+end)/2);
            build((2*node),start,mid);
            build((2*node)+1,mid+1,end);
            y[node][0]=y[(2*node)][0]+y[(2*node)+1][0];
            y[node][1]=y[(2*node)][1]+y[(2*node)+1][1];
            initialize_up(node);
        }
    }

    int main()
    {
        ios_base::sync_with_stdio(false);
        for(i=0;i<100001;i++)
        {
            b[i][0]=0;
            b[i][1]=0;
        }
        b[2][0]=1;
        b[5][1]=1;
        for(i=4;i<=100000;i+=2)
            b[i][0]=(b[2][0]+b[(i/2)][0]);
        for(i=10;i<=100000;i+=5)
            b[i][1]=(b[5][1]+b[(i/5)][1]);
        c[0][0]=0;
        c[0][1]=0;
        for(i=1;i<=100000;i++)
        {
            c[i][0]=c[i-1][0]+b[i][0];
            c[i][1]=c[i-1][1]+b[i][1];
        }
        cin>>t;
        while(t--)
        {
            cin>>n>>m;
            countz=0;
            for(i=0;i<n;i++)
            {
                cin>>A;
                if(A<=100000)
                {
                    a[i][0]=b[A][0];
                    a[i][1]=b[A][1];
                }
                else
                {
                    a[i][0]=0;
                    a[i][1]=0;
                    while((A%2)==0)
                    {
                        a[i][0]++;
                        A/=2;
                        if(A<=100000)
                        {
                            a[i][0]+=b[A][0];
                            a[i][1]+=b[A][1];
                            A=1;
                        }
                    }
                    while((A%5)==0)
                    {
                        a[i][1]++;
                        A/=5;
                        if(A<=100000)
                        {
                            a[i][0]+=b[A][0];
                            a[i][1]+=b[A][1];
                            A=1;
                        }
                    }
                }
            }
            build(1,0,n-1);
            while(m--)
            {
                cin>>p>>l>>r;
                countx=0;
                county=0;
                l--;
                r--;
                if(p==1)
                {
                    count2=0;
                    count5=0;
                    cin>>A;
                    if(A<=100000)
                    {
                        count2=b[A][0];
                        count5=b[A][1];
                    }
                    else
                    {
                        while((A%2)==0)
                        {
                            count2++;
                            A/=2;
                            if(A<=100000)
                            {
                                count2+=b[A][0];
                                count5+=b[A][1];
                                A=1;
                            }
                        }
                        while((A%5)==0)
                        {
                            count5++;
                            A/=5;
                            if(A<=100000)
                            {
                                count2+=b[A][0];
                                count5+=b[A][1];
                                A=1;
                            }
                        }
                    }
                    updatex(1,0,n-1);
                }
                else if(p==2)
                {
                    count2=0;
                    count5=0;
                    cin>>A;
                    if(A<=100000)
                    {
                        count2=b[A][0];
                        count5=b[A][1];
                    }
                    else
                    {
                        while((A%2)==0)
                        {
                            count2++;
                            A/=2;
                            if(A<=100000)
                            {
                                count2+=b[A][0];
                                count5+=b[A][1];
                                A=1;
                            }
                        }
                        while((A%5)==0)
                        {
                            count5++;
                            A/=5;
                            if(A<=100000)
                            {
                                count2+=b[A][0];
                                count5+=b[A][1];
                                A=1;
                            }
                        }
                    }
                    updatey(1,0,n-1);
                }
                else
                    value(1,0,n-1);
                countz+=min(countx,county);
            }
            cout<<countz<<"\n";
        }
        return 0;
    }
