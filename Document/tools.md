/*
这是与道具魔法相关的代码
*/
//进行道具购买
void BuyProp(int number)
{
	int n;
	printf("来到了道具屋。\n");
	printf("1: 炸弹 %d点卷\n2: 路障 %d点卷\n3: 机器娃娃 %d点卷\n4:退出\n",price[1],price[2],price[3]);
	scanf("%d",&n);
	if(n>=1 && n<=3)
	{
		if(player[number].ticket>=100)
		{
			player[number].ticket-=100;
			player[number].prop[n]++;
			printf("购买成功！\n");
		}
		else 
		{
			printf("点卷并不够。");
		}
	}
}

//礼品屋
void GetGift(int number)
{
	int n;
	printf("来到了礼品屋。\n");
	printf("1: +500元\n2: +100点卷\n3: 财神附身\n4:退出\n");
	scanf("%d",&n);
	if(n==4)return ;
	if(n==1)
	{
		player[number].money+=500;
		printf("获得500元！\n");
	}
	else if(n==2)
	{
		player[number].ticket+=100;
		printf("获得100点卷\n");
	}
	else if(n==3)
	{
		player[number].god=1;
		player[number].time=TIME;
		printf("财神附身%d回合.\n",TIME);
	}
}









//使用道具，
void UseProp(int number)
{
	int i,n;
	int pos=player[number].position;
	printf("您的道具如下:\n");
	printf("1:炸弹:%d\n",player[number].prop[1]);
	printf("2:路障:%d\n",player[number].prop[2]);
	printf("3:机器娃娃:%d\n",player[number].prop[3]);
	printf("4:返回\n");
	scanf("%d",&n);
	if(n==4)return ;
	if(n==3)
	{
		if(player[number].prop[n])
		{
			player[number].prop[n]--;
			for(i=-6;i<=6;i++)
				map[pos].parameter=0;
			printf("使用机器娃娃，已清除前后6格路障与炸弹。\n");
		}
		else 
		{
			printf("您没有这个道具。\n");
			UseProp(number);
		}
	}
	else 
	{

		
		if(player[number].prop[n])
		{
			player[number].prop[n]--;
			printf("选择一个位置放置：\n");
			scanf("%d",&n);
			map[n].parameter=n;
			printf("成功在%d位置放置一个");
			if(n==1)printf("炸弹\n");
			else printf("路障\n");
		}
		else 
		{
			printf("您没有这个道具。");
			UseProp(number);
		}
	}
}
