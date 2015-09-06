
#这是与地图相关的代码
#内容有升级土地、购买土地、判断是否有土地判断是否有土地、出售土地
//升级土地
void LevelUp(int number)
{
	int n;
	int pos=player[number].position;
	if(player[number].money<map[pos].price && player[number].god!=1)
	{
		printf("这是您自己的土地，然而您并没有足够的钱升级。");
		return ;
	}
	if( map[pos].level==MAXLEVEL )
	{
		printf("土地已至最大等级。");
		return ;
	}
	printf("这是您自己的土地，是否升级？(%d元)\n1:是\n2:否\n");
	scanf("%d",&n);

	if(n==1)
	{
		map[pos].level++;


		if(player[number].god==1)
		{
			printf("财神附体，并不用花钱就升级成功了。\n");
		}


		player[number].money-=map[pos].price;
		printf("花费%d元,升级成功。");
	}
	if(n==2)return ;
	getchar();
}

//购买土地
void BuyLand(int number)
{
	int n;
	int pos=player[number].position;
	
	
	if(player[number].money<map[pos].price && player[number].god!=1)
	{
		printf("你并没有足够的钱来购买这块土地。");
		return ;
	}
	
	printf("是否购买这块土地？(%d元)\n1:是\n2:否\n",map[pos].price);
	scanf("%d",&n);
	
	if(n==1)
	{
		
			
		

		map[pos].level=0;
		map[pos].player=number;

		if(player[number].god==1)
		{
			printf("财神附体，并不用花钱就购买成功了。\n");
		}

		player[number].money-=map[pos].price;
		printf("花费%d元,购买成功。余额：%d元\n",map[pos].price,player[number].money);
	}
	if(n==2)return ;
}




//判断是否有土地
int HaveLand(int number)
{
	int i;
	for(i=1;i<=num_cell;i++)
		if(map[i].player==number)
		{
			return 1;
		}
	
	return 0;
}


//出售土地
void SellLand(int number)
{
	int i,n;
	int land[MAXLAND+1];
	int num=0;
	for(i=1;i<=num_cell;i++)
	{
		if(map[i].player==number)
		{
			num++;
			land[num]=i;
		}
	}
	printf("您的土地如下，请选择出售:\n");
	for(i=1;i<=num;i++)
	{
		printf("%d:位置:%d,价格:%d,等级:%d\n",i,land[i],map[land[i]].price*(map[land[i]].level+1),map[land[i]].level);
	}
	scanf("%d",&n);
	map[land[n]].player=0;
	
	if(player[number].god==2){printf("衰神附体，毫无收益。\n");return;}
	player[number].money+=map[land[i]].price*(map[land[i]].level+1);
	printf("玩家%d出售%d位置的土地获得%d元\n",number,land[n],map[land[i]].price*(map[land[i]].level+1));
}

