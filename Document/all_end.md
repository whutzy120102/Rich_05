#define MAXPLAYER 4					//最多几个玩家
#define MAXPROP	3					//有几种道具
#define MAXMAP 999					//地图大小。。。。
#define HOSPITAL 0					//医院位置
#define REST 3						//被炸弹炸了休息回合数

#define TIME 3						//附体回合数
#define MAXLEVEL 3					//房屋最大等级

#define MAXLAND 100					//个人拥有房屋最大数目

#include<stdio.h>
#include <time.h>
#include <stdlib.h>
#include <conio.h>
#include<windows.h>



struct PLAYER{
	int alive;						//被选用且还有钱
	int rest;						//休息回合
	int position;					//位置
	int money;						//金钱
	int ticket;						//点卷
	int prop[MAXPROP];				//各道具数目1:炸弹  2:路障 3:机器娃娃
	int god;						//附体神 1:财神 2:衰神
	int time;						//附体时间
}player[MAXPLAYER+1];
struct MAP{
	int price;						//价格
	int player;						//拥有者
	int level;						//房屋等级
	int category;					//类别 0:土地 1:礼品屋 2:道具屋 3:医院 
	int parameter;					//参数 0:无 1:炸弹 2:路障
	int next;						//下一格
}map[MAXMAP];



int price[MAXPROP+1];
int num_player;
int num_cell;

int MONEY; 				//初始金钱
int TICKET; 				//初始点卷



/*
  地图显示规则  由3行4列字符表示一格。
  第一行。
    前两列是位置编号，
    第三列第四列为类别(T表示道具屋，G表示礼品屋，L,M,N,O表示土地+等级(若被购买，则之后加上拥有者编号)，H表示医院)
  第二行。为在这个位置上的角色列表。
  第三行。前三位表示土地价格，第四列表示道具(@表示有炸弹，#表示有路障)
*/
int toshow[40][400];//显示地图用
int color[40][400];
void MapToShow(int x,int y,int k)
{
	x=1+x*4;
	y=1+y*5+3;

	
	//
	if(map[k].category==0)
	{
		toshow[x][y-1]='L'+map[k].level;
		toshow[x+2][y-3]=map[k].price/100+'0';
		toshow[x+2][y-2]=map[k].price/10%10+'0';
		toshow[x+2][y-1]=map[k].price%10+'0';
	}



	if(map[k].category==1)toshow[x][y]='G';
	if(map[k].category==2)toshow[x][y]='T';
	if(map[k].category==3)toshow[x][y]='H';

	//位置编号
	y-=3;
	toshow[x][y]=k/10+'0';
	toshow[x][y+1]=k%10+'0';



	//边框
	toshow[x-1][y]='-';
	toshow[x-1][y+1]='-';
	toshow[x-1][y+2]='-';
	toshow[x-1][y+3]='-';
	toshow[x+3][y]='-';
	toshow[x+3][y+1]='-';
	toshow[x+3][y+2]='-';
	toshow[x+3][y+3]='-';
	toshow[x][y-1]='|';
	toshow[x][y+4]='|';
	toshow[x+1][y-1]='|';
	toshow[x+1][y+4]='|';
	toshow[x+2][y-1]='|';
	toshow[x+2][y+4]='|';
	//角色
	int tcolor;


	if(map[k].player==0)tcolor=FOREGROUND_INTENSITY | FOREGROUND_INTENSITY | FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE;
	if(map[k].player==1)tcolor=FOREGROUND_INTENSITY | FOREGROUND_GREEN;
	if(map[k].player==2)tcolor=FOREGROUND_INTENSITY | FOREGROUND_RED | FOREGROUND_GREEN;
	if(map[k].player==3)tcolor=FOREGROUND_INTENSITY | FOREGROUND_BLUE;
	if(map[k].player==4)tcolor=FOREGROUND_INTENSITY | FOREGROUND_RED | FOREGROUND_BLUE;


	int i,j;
	for(i=1;i<=MAXPLAYER;i++)
		if(player[i].position==k && player[i].alive)	
		{
			toshow[x+1][y+i-1]='0'+i;
			color[x+1][y+i-1]=tcolor;
		}
	//道具
	x+=2;y+=3;
	if(map[k].parameter==1)
		toshow[x][y]='@';
	if(map[k].parameter==2)
		toshow[x][y]='#';


	

	
	x-=2;y-=3;
	
	for(i=x;i<=x+2;i++)
		for(j=y;j<=y+3;j++)
			color[i][j]=tcolor;



	if(toshow[x+1][y]=='1')color[x+1][y]=FOREGROUND_INTENSITY | FOREGROUND_GREEN;
	if(toshow[x+1][y+1]=='2')color[x+1][y+1]=FOREGROUND_INTENSITY | FOREGROUND_RED | FOREGROUND_GREEN;
	if(toshow[x+1][y+2]=='3')color[x+1][y+2]=FOREGROUND_INTENSITY | FOREGROUND_BLUE;
	if(toshow[x+1][y+3]=='4')color[x+1][y+3]=FOREGROUND_INTENSITY | FOREGROUND_RED | FOREGROUND_BLUE;


	x+=2;y+=3;
	if(map[k].parameter==1)
		color[x][y]=FOREGROUND_INTENSITY | FOREGROUND_RED;
	if(map[k].parameter==2)
		color[x][y]=FOREGROUND_INTENSITY | FOREGROUND_RED;
	


}
//显示地图
void ShowMap()
{


	int i,j;
	for(i=0;i<=24;i++)
		for(j=0;j<=60;j++)
		{
			toshow[i][j]=' ';
			color[i][j]=FOREGROUND_INTENSITY |FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE;
		}
	for(i=0;i<=10;i++)
		MapToShow(1,i,i);
	MapToShow(2,10,11);
	MapToShow(3,10,12);
	MapToShow(4,10,13);
	MapToShow(2,1,26);
	MapToShow(3,1,25);
	MapToShow(4,1,24);
	for(i=0;i<10;i++)
		MapToShow(5,i+1,23-i);
	for(i=0;i<=6*4;i++)
	{
		for(j=0;j<=12*5;j++)
		{	
			SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),color[i][j]);
			printf("%c",toshow[i][j]);	
		}
		printf("\n");
			
	}
}


void Kill(int number)
{
	int i;
	for(i=1;i<=num_cell;i++)
		if(map[i].player==number && map[i].category==0)
		{
			
			map[i].player=0;
			map[i].level=0;
		}
	num_player--;
	player[number].alive=0;
}
//获得骰子点数
int GetPoint()
{
	
	return rand()%6+1;
}


//前进,这里会处理炸弹与路障,玩家，前进点数
void Move (int number,int point)
{
	while (point--)
	{
		player[number].position=map[player[number].position].next;
		if(map[player[number].position].parameter==1)
		{
			map[player[number].position].parameter=0;
			player[number].position=HOSPITAL;
			player[number].rest=REST;
			
			printf("被炸弹炸伤送至医院。\n");
			return;
		}
		else if(map[player[number].position].parameter==2)
		{
			map[player[number].position].parameter=0;
			printf("被路障阻拦。\n");
			return ;
		}
	}
}












//进行道具购买
void BuyProp(int number)
{
	int n;
	printf("来到了道具屋。您有%d点卷:\n",player[number].ticket);
	printf("1: 炸弹 %d点卷\n2: 路障 %d点卷\n3: 机器娃娃 %d点卷\n4:退出\n",price[1],price[2],price[3]);
	scanf("%d",&n);
	if(n>=1 && n<=3)
	{
		if(player[number].ticket>=100)
		{
			player[number].ticket-=100;
			player[number].prop[n]++;
			printf("购买成功！\n");
			BuyProp(number);
		}
		else 
		{
			printf("点卷并不够。\n");
			BuyProp(number);
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



//升级土地
void LevelUp(int number)
{
	int n;
	int pos=player[number].position;
	if(player[number].money<map[pos].price && player[number].god!=1)
	{
		printf("这是您自己的土地，然而您并没有足够的钱升级。\n");
		return ;
	}
	if( map[pos].level==MAXLEVEL )
	{
		printf("土地已至最大等级。\n");
		return ;
	}
	printf("这是您自己的土地，是否升级？(%d元)\n1:是\n2:否\n",map[pos].price);
	scanf("%d",&n);

	if(n==1)
	{
		map[pos].level++;


		if(player[number].god==1)
		{
			printf("财神附体，并不用花钱就升级成功了。\n当前等级：%d\n",map[pos].level);
			return ;
		}

		
		player[number].money-=map[pos].price;
		printf("花费%d元,升级成功。\n当前等级：%d\n",map[pos].price,map[pos].level);
	}
	if(n==2)return ;
}

//购买土地
void BuyLand(int number)
{
	int n;
	int pos=player[number].position;
	
	
	if(player[number].money<map[pos].price && player[number].god!=1)
	{
		printf("你并没有足够的钱来购买这块土地。\n");
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
			return ;
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
	map[land[n]].level=0;
	
	if(player[number].god==2){printf("衰神附体，毫无收益。\n");return;}
	player[number].money+=map[land[n]].price*(map[land[n]].level+1);
	printf("玩家%d出售%d位置的土地获得%d元\n",number,land[n],map[land[n]].price*(map[land[n]].level+1));
}




//付税
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
			Kill(number);
			return ;
		}
	}
	player[number].money-=tax;
	player[ map[pos].player ].money+=tax;
	printf("玩家%d付%d元给玩家%d。\n",number,tax,map[pos].player);
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
			for(i=1;i<=10;i++)
			{
				if(pos+i<=num_cell)
					map[pos+i].parameter=0;
				else
					map[pos+i-num_cell].parameter=0;
			}
			printf("使用机器娃娃，已清除前方10格路障与炸弹。\n");
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
			scanf("%d",&pos);
			map[pos].parameter=n;
			printf("成功在%d位置放置一个",pos);
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


//更改地图元素，位置，下一个位置，类别，参数
void ChangeMap(int position,int next,int category,int price)
{
	if(next!=-1)map[position].next=next;
	if(category!=-1)map[position].category=category;
	if(price!=-1)map[position].price=price;
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



	FILE *fp;
	fp=fopen("map.txt","r");


	//从文件设置地图
	int num;
	fscanf(fp,"%d",&num);
	int position,next,category,pric;
	for(i=1;i<=num;i++)
	{
		fscanf(fp,"%d%d%d%d",&position,&next,&category,&pric);
		ChangeMap(position,next,category,pric);
	}
	fclose(fp);



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
	printf("初始金钱：");
	scanf("%d",&MONEY);
	printf("初始点卷：");
	scanf("%d",&TICKET);





	int now=0;
	
	now=GetNextPlayer(now);
	while(num_player>1)
	{
		ShowMap();
		printf("当前玩家:%d\n",now);
		printf("    位置:%d\n",player[now].position);
		printf("    金钱:%d\n",player[now].money);
		printf("    点卷:%d\n",player[now].ticket);
		if(player[now].god==1)
			printf("财神附体剩余回合数:%d\n",player[now].time);
		if(player[now].rest)
		{
			printf("还在休息中。。剩余%d回合。\n",player[now].rest);
			player[now].rest--;
			continue;
		}
		printf("1:掷骰子\n");
		printf("2:使用道具\n");
		printf("3:出售土地\n");
		printf("4:投降\n");
		scanf("%d",&n);
		if(n==1)
		{
			int t=GetPoint();
			printf("骰子点数：%d\n",t);
			Move(now,t);
			printf("当前位置:%d\n",player[now].position);
			Check(now);

			

			printf("玩家%d行动结束。\n",now);
			if(player[now].god==1)
			{	player[now].time--;
				if(player[now].time<=0)
				{
					player[now].god=0;
					player[now].time=0;
					printf("财神附体效果结束。\n");
				}
			}

/*
			char st[2];
			gets(st);*/
			getch();
			now=GetNextPlayer(now);
		}
		if(n==2)
		{
			UseProp(now);
		}
		if(n==3)
		{
			if(HaveLand(now)==false)
				printf("您没有任何土地。\n");
			else
				SellLand(now);
		}
		if(n==4)
		{
			Kill(now);
		}
	}

	//游戏结束
	for(i=1;i<=MAXPLAYER;i++)
		if(player[i].alive)
			printf("游戏结束，玩家%d获胜！\n\n\n",i);
}

//修改地图


int main()
{
	int t;
	
	srand((unsigned) time(NULL)); 
	printf("1.开始游戏\n2.退出\n");
	scanf("%d",&t);
	if(t==1)Begin();
	return 0;
}





