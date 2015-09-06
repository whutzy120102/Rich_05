/*
这是与玩家相关的代码
*/
//前进,这里会处理炸弹与路障,玩家，前进点数
void Move (int number,int point)
{
	while (point--)
	{
		player[number].position=map[player[number].position].next;
		if(map[player[number].position].parameter==1)
		{
			player[number].position=HOSPITAL;
			player[number].rest=REST;
			map[player[number].position].parameter=0;
			printf("被炸弹炸伤送至医院。\n");
		}
		else if(map[player[number].position].parameter==2)
		{
			map[player[number].position].parameter=0;
			printf("被路障阻拦。\n");
			return ;
		}
	}
}
//玩家付过路费
void Tax(int number)
{
	if(player[number].god==1)
	{
		printf("财神附体，免过路费。\n");
		return ;
	}
	int pos=player[number].position;
	int tax=map[pos].price*(map[pos].level+1)/5;
	while(player[number].money<tax && player[number].alive)
	{
		printf("你。。。钱不够。。。");
		if(HaveLand(number))
			SellLand(number);
		else
		{
			player[number].alive=false;
			num_player--;
			return ;
		}
	}
	player[number].money-=tax;
	player[ map[pos].player ].money+=tax;
	printf("%d付%d元给%d。",number,tax,map[pos].player);
	getchar();
}
//判定目标位置
void Check(int number)
{

	int pos=player[number].position;
	if(map[pos].category==0)
	{//走到土地位置
		if(map[pos].player==number)
			//自己的土地
			LevelUp(number);
		else if(map[pos].player==0)
			//土地未被购买
			BuyLand(number);
		else 
			//别人的土地
			Tax(number);
	}
	else if(map[pos].category==1)
	{//礼品屋
		GetGift(number);
	}
	else if(map[pos].category==2)
	{//道具屋
		BuyProp(number);
	}
}


//获得下一个有效玩家
int GetNextPlayer(int number)
{
	number++;
	if(number>MAXPLAYER)number=1;
	while (player[number].alive==0)
	{
		number++;
		if(number>MAXPLAYER)number=1;
	}
	return number;
}
