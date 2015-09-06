/*
这是与游戏开始相关的代码
*/
int main()
{
	int t;

	srand((unsigned) time(NULL)); 

	printf("1.开始游戏\n2.修改地图\n3.退出\n");
	scanf("%d",&t);
	if(t==1)Begin();
	else if(t==2)ModifyMap();
	return 0;
}
//开始游戏
void Begin()
{
	int n;
	int i;
	Init();
	printf("玩家数目:");
	scanf("%d",&num_player);
	printf("选择的角色:");
	for(i=1;i<=num_player;i++)
	{
		scanf("%d",&n);
		player[n].alive=1;
	}
	int now=0;
	ShowMap();
	now=GetNextPlayer(now);
	while(num_player>=1)
	{
		printf("当前玩家:%d\n",now);
		printf("    位置:%d\n",player[now].position);
		printf("    金钱:%d\n",player[now].money);
		printf("    点卷:%d\n",player[now].ticket);
		printf("1:掷骰子\n");
		printf("2:使用道具\n");
		printf("3:出售土地\n");
		scanf("%d",&n);
		if(n==1)
		{
			int t=GetPoint();
			printf("骰子点数：%d\n",t);
			Move(now,t);
			printf("当前位置:%d\n",player[now].position);
			Check(now);

			printf("玩家%d行动结束。\n\n\n",now);
			now=GetNextPlayer(now);
		}
		if(n==2)
		{
			UseProp(now);
		}
		if(n==3)
		{
			if(HaveLand(now)==false)
				printf("您没有任何土地。");
			else
				SellLand(now);
		}

	}

	//游戏结束
}
//初始化
void Init()
{
	int i,j;
	num_cell=26;
	for(i=1;i<=MAXPLAYER;i++)
	{
		player[i].alive=0;
		player[i].god=0;
		player[i].money=MONEY;
		player[i].position=0;
		for(j=1;j<=MAXPROP;j++)
			player[i].prop[j]=0;
		player[i].rest=0;
		player[i].ticket=TICKET;
	}
	for(i=0;i<=num_cell;i++)
	{
		map[i].category=0;
		map[i].level=0;
		map[i].next=i+1;
		map[i].parameter=0;
		map[i].player=0;
		map[i].price=100;
	}
	map[num_cell].next=1;

	map[HOSPITAL].category=3;

	//道具价格
	price[1]=100;
	price[2]=100;
	price[3]=100;


	//设置地图
	printf("设置地图...\n");
	int num;
	scanf("%d",&num);
	int position,next,category,pric;
	for(i=1;i<=num;i++)
	{
		scanf("%d%d%d%d",&position,&next,&category,&pric);
		ChangeMap(position,next,category,pric);
	}




}
