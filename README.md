# shubham_assignment
#include <bits/stdc++.h>
using namespace std;

struct Interval
{
	int low,
	high;
};

struct ITNode
{
	Interval *i;
	int max;
	ITNode *left, *right;
};

int mx(int a,int b,int c)
{
    if(a>b&&a>c)return a;
    else if(b>c&&b>a)return b;
    return c;
}

ITNode * newNode(Interval i)
{
	ITNode *temp = new ITNode;
	temp->i = new Interval(i);
	temp->max = i.high;
	temp->left = temp->right = NULL;
};

ITNode *insert(ITNode *root, Interval i)
{
	if (root == NULL)
		return newNode(i);
	int l = root->i->low;
	if (i.low < l)
		root->left = insert(root->left, i);


	else
		root->right = insert(root->right, i);


	if (root->max < i.high)
		root->max = i.high;

	return root;
}
//////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
ITNode *delet(ITNode *ptr, Interval i)
     {

         ITNode* tmp,*succ;
         if(ptr==NULL)
         {
             cout<<"interval not found";
             return ptr;
         }
         if(i.low< ptr->i->low)
         {
             ptr->left= delet(ptr->left,i);
             if(ptr->left!=NULL&&ptr->right!=NULL)
                ptr->max=mx(ptr->left->max,ptr->i->high,ptr->right->max);
                  else if(ptr->left==NULL&&ptr->right==NULL)
                      ptr->max=ptr->i->high;
            else if(ptr->left==NULL)
                 ptr->max=mx(-1,ptr->i->high,ptr->right->max);
            else if(ptr->right==NULL)
                 ptr->max=mx(ptr->left->max,ptr->i->high,-1);
             else ptr->max=ptr->i->high;

         }

         else if(i.low> ptr->i->low)
              {
                  ptr->right=delet(ptr->right,i);
                  if(ptr->left!=NULL&&ptr->right!=NULL)
                ptr->max=mx(ptr->left->max,ptr->i->high,ptr->right->max);
                else if(ptr->left==NULL&&ptr->right==NULL)
                      ptr->max=ptr->i->high;
            else if(ptr->left==NULL)
                 ptr->max=mx(-1,ptr->i->high,ptr->right->max);
            else if(ptr->right==NULL)
                 ptr->max=mx(ptr->left->max,ptr->i->high,-1);
             else ptr->max=ptr->i->high;
              }
         else if(i.low==ptr->i->low&&i.high==ptr->i->high)
                  {
                      // key to delete is found;
                      if(ptr->left!=NULL&&ptr->right!=NULL)
                      {
                          succ=ptr->right;
                          while(succ->left)
                              succ=succ->left;
                          ptr->i=succ->i;
                          Interval k;
                          k.low=succ->i->low;
                          k.high=succ->i->high;
                          ptr->right=delet(ptr->right,k);
                          if(ptr->right!=NULL)
                            ptr->max=mx(ptr->left->max, ptr->right->max,-1);
                          else
                             ptr->max=mx(ptr->left->max, -1,-1);
                      }
                            else
                         {
                              tmp=ptr;
                              if(ptr->right!=NULL)
                               {
                                ptr=ptr->right;

                                }
                                 else if( ptr->left!=NULL)
                                             ptr=ptr->right;
                                          else
                                              ptr=NULL;

                                   free(tmp);
                          }

              }
       return ptr;
}
////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

bool doOVerlap(Interval i1, Interval i2)
{
	if (i1.low <= i2.high && i2.low <= i1.high)
		return true;
	return false;
}

Interval *overlapSearch(ITNode *root, Interval i)
{

	if (root == NULL) return NULL;

	// If given interval overlaps with root
	if (doOVerlap(*(root->i), i))
		return root->i;

	if (root->left != NULL && root->left->max >= i.low)
		return overlapSearch(root->left, i);


	return overlapSearch(root->right, i);
}

 void inorder(ITNode *root)
{
	if (root == NULL) return;

	inorder(root->left);

	cout << "[" << root->i->low << ", " << root->i->high << "]"
		<< " max = " << root->max << endl;

	inorder(root->right);
}


int main()
{

	Interval ints[] =
	    {
	      {15, 20}, {10, 30}, {17, 19},
		{5, 20}, {12, 15}, {30, 40}
		};
   // cout<<"the intervals in which program is working, is {15, 20}, {10, 30}, {17, 19},{5, 20}, {12, 15}, {30, 40}\n";
	int n = sizeof(ints)/sizeof(ints[0]);
	ITNode *root = NULL;
	for (int i = 0; i < n; i++)
		root = insert(root, ints[i]);
		int flag=1,cnt=5;
     {
         int press;
	cout<<"  if you want to ,  delete enter 1,  enter  2 for search ,enter 3 for insert...4 for exit \n";
	cin>>press;
	if(press==1)
       {  int num;
           cout<<"enter index of interval,you  want to delete (0-based indexing)";
          cin>>num;
           delet(root,ints[num]);
           cout<<"\n";
          // cout<<"node deleted/n";
           cout<<"nodes are now these:-\n";
           inorder(root);
       }
   else if(press==2)

	{
	    //int num;
	   // cout<<"input interval to search( 0 based indexing)\n";
	   // cin>>num;
	     cout<<"input  interval that you want to see\n";
	     int a,b;
	     cin>>a>>b;
	    Interval x = {a,b};
	    cout << "\nSearching for interval [" << x.low << "," << x.high << "]";
	Interval *res = overlapSearch(root, x);
	if (res == NULL)
		cout << "\nNo Overlapping Interval";
	else
		cout << "\nOverlaps with [" << res->low << ", " << res->high << "]";
	}
   else if(press==3)
   {
       cout<<"write  interval to insert";
        int a, b;
        cin>>a>>b;
        ints[++cnt]={a,b};
        insert(root,ints[cnt]);
        inorder(root);
   }
	else flag=0;
     }
	return 0;
}
