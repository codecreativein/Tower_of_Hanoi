#define _CRT_SECURE_NO_WARNINGS
#define LEFT_X 107
#define MEDIUM_X 320
#define RIGHT_X 533
#include<graphics.h>
#include<conio.h>
#include<iostream>
#include<stack>
#include<math.h>
#include<stdlib.h>
using namespace std;


/***
 * When I wrote this, only God and I understood what I was doing
 * Now, God only knows
 *                    _ooOoo_
 *                   o8888888o
 *                   88" . "88
 *                   (| -_- |)
 *                    O\ = /O
 *                ____/`---'\____
 *              .   ' \\| |// `.
 *               / \\||| : |||// \
 *             / _||||| -:- |||||- \
 *               | | \\\ - /// | |
 *             | \_| ''\---/'' | |
 *              \ .-\__ `-` ___/-. /
 *           ___`. .' /--.--\ `. . __
 *        ."" '< `.___\_<|>_/___.' >'"".
 *       | | : `- \`.;`\ _ /`;.`/ - ` : | |
 *         \ \ `-. \_ __\ /__ _/ .-` / /
 * ======`-.____`-.___\_____/___.-`____.-'======
 *                    `=---='
 *.............................................
 *          佛祖保佑             永无BUG
 *
 **/

//--------------------------------classes && the params--------------------//
int width = 640;
int height = 480;
int base_y = height - 30;
int top_y[3] = { base_y,base_y,base_y };
int height_of_line = 6;//book的厚度
double k_vel = 50;//follow 速度的系数
double k_collide = 0.1;// k_collide <<1 下落撞击反弹的参数
int num_collide = 3;//撞击的次数
double g=50;//重力加速度
int cent_plate_x[3] = {107,320,533};//每个盘子中央的x坐标
int moving_x;//运动的book暂时用的中心的横纵坐标
int moving_y;
int plate;//上一个锁定的book所在的栈
char c=0;//捕获键盘的键值
MOUSEMSG msg;


//汉诺塔的某一层
// the pre_init of the CLASS BOOK
class BOOK {
public:
	int width;
	COLORREF color;
	int centofx;
	int centofy;
public:
	int i;
	int left_up[2];
	int right_down[2];
	double v_x;
	double v_y;
	BOOK() {}
	//set width, i, color,
	BOOK(int i_){
		i = i_;
		width = 20 + i_ * 10;
		color = 0xffffff;
		for (int start = 0; start < i; start++) {
			switch (start % 3) {
			case 0:color -= 0x000044; break;
			case 1:color -= 0x004400; break;
			case 2:color -= 0x440000; break;
			}
		}
	}
	//  [0] is x, [1] is y
	//用于规范鼠标点击的范围
	void set_sq() {
		left_up[0] = centofx - width/2;
		left_up[1] = centofy - height_of_line/2;
		right_down[0] = centofx + width / 2;
		right_down[1] = centofy + height_of_line / 2;
	}
	bool if_in_cube(int x, int y) {
		set_sq();
		if (x <= right_down[0] && x >= left_up[0] && y <= right_down[1] && y >= left_up[1]) { 
			return true; 
		}
		else { return false; }
	}
	void change_i(int i_) {
		i = i_;
		width = 20 + i_ * 10;
		color = 0xffffff;
		for (int start = 0; start < i; start++) {
			switch (start % 3) {
			case 0:color -= 0x000044; break;
			case 1:color -= 0x004400; break;
			case 2:color -= 0x440000; break;
			}
		}
	}
	void set_cent(int x_, int y_) {
		centofx = x_;
		centofy = y_;
		set_sq();
	}

	//when initializing the hannuota use the function 
	void draw() {
		setlinestyle(PS_SOLID, height_of_line);
		setwritemode(R2_XORPEN);
		setlinecolor(color);
		line(centofx-width/2,centofy,centofx+width/2,centofy);
	}

	void draw(int x_, int y_) {
		setlinestyle(PS_SOLID, height_of_line);
		setwritemode(R2_XORPEN);
		setlinecolor(color);
		line(x_ - width / 2, y_, x_ + width / 2, y_);
	}
	
	//挪动前使用
	void temp_transfer_to() {
		moving_x = centofx;
		moving_y = centofy;
	}
	//挪动后使用
	void temp_transfer_back() {
		centofx = moving_x;
		centofy = moving_y;
	}

	void move_and_draw(double vx_, double vy_, double delta_time) {
		draw(moving_x, moving_y);
		moving_x += int(vx_ * delta_time);
		moving_y += int(vy_ * delta_time);
		draw(moving_x,moving_y);
	}
	void move_and_draw(int x_, int y_) {
		draw(moving_x, moving_y);
		draw(moving_x = x_, moving_y = y_);
	}

	// the book follow the mouse when the mouse touch the book
	//书在鼠标拖动时跟随鼠标
	void draw_follow(int mouse_x, int mouse_y, double delta_time) {
		v_x = k_vel * (mouse_x - moving_x);//k * int((mouse_x-centofx)*abs(mouse_x-centofx));
		v_y = k_vel * (mouse_y - moving_y);//k * int((mouse_y - centofy) * abs(mouse_y - centofy)); 
		move_and_draw(int(moving_x+v_x*delta_time),int(moving_y+v_y*delta_time));
	}

	// drop the book when the !is_follow automatically
	//当鼠标放开书时自动回落位置
	void drop_x(int x_, double delta_time) {
		if (abs(x_ - moving_x) >= 5) {
			v_x = k_vel * int(x_ - moving_x);
			move_and_draw(v_x, 0, delta_time);
		}
		else {
			move_and_draw(x_,moving_y);
		}
	}
	//run only once
	inline void vy_to_zero() {
		v_y = 0;
	}
	//run every time
	void drop_y(int y_, double delta_time) {
		v_y += g * delta_time;
		if (y_ - moving_y > int(v_y * delta_time)) {
			moving_y += int(v_y * delta_time);
		}
		else {
			moving_y = y_;
		}
		move_and_draw(moving_x, moving_y);
	}
	
	void drop_full(int x_, int y_, double delta_time) {
		while (x_ != moving_x) {
			drop_x(x_, delta_time);
			Sleep(int(delta_time * 1000));
		}
		while (y_ != moving_y) {
			drop_y(y_,delta_time);
			Sleep(int(delta_time * 1000));
		}
	}
	void direct_drop(int x, int y) {
		move_and_draw(x, y);
		moving_x = x;
		moving_y = y;
	}
};

BOOK temp;
stack<BOOK> books[3];

inline void draw_all_lines() {
	line(0, base_y, 640, base_y);
	line(213, 0, 213, 480);
	line(427, 0, 427, 480);
}

void change_bool(bool* example) {
	if (*example)*example = false;
	else *example = true;
}

void show_int(int x_, int y_, int i) {
	char temp[5];
	_itoa(i, temp, 10);
	outtextxy(x_, y_, temp[0]);
}

//-------------------------------------------------------------------
//functions
//汉诺塔初始化
void books_init(int numofbooks) {
	for (int i = numofbooks; i > 0; i--) {
		temp.change_i(i);
		temp.set_cent(LEFT_X, top_y[0] - height_of_line / 2);
		//最左列落塔
		books[0].push(temp);
		temp.draw();
		top_y[0] -= height_of_line;
		Sleep(10);
	}
	cout << endl; cout << endl;
}

//汉诺塔逻辑关系
void move_books(int numofbooks) {
	bool is_follow = false;
	bool is_drop = false;
	short tempmousex;
	short tempmousey;
	//BeginBatchDraw();
	while (c!=27)
	{
		if (_kbhit()) {
			c = _getch();
		}
		msg = GetMouseMsg();
		switch (msg.uMsg)
		{
		case WM_LBUTTONDOWN: {     //鼠标左键
			//该掉落了
			if (is_follow) {
				change_bool(&is_follow);
				books[plate].top().vy_to_zero();
				//落回原处
				if (plate == 3 * msg.x / width) {
					books[plate].top().direct_drop(cent_plate_x[plate], books[plate].top().centofy);
				}
				//落到别的栈
				else {
					// 落到空栈
					if (books[3 * msg.x / width].size() == 0) {
						books[plate].top().direct_drop(cent_plate_x[3 * msg.x / width], base_y - height_of_line / 2);
						books[plate].top().temp_transfer_back();
						books[3 * msg.x / width].push(books[plate].top());
						books[plate].pop();
					}
					// 落到有塔的栈中
					else {
						//判断大塔是不是在小塔下边
						if (books[plate].top().i < books[3 * msg.x / width].top().i) {
							books[plate].top().direct_drop(cent_plate_x[3 * msg.x / width], books[3 * msg.x / width].top().centofy - height_of_line);
							books[plate].top().temp_transfer_back();
							books[3 * msg.x / width].push(books[plate].top());
							books[plate].pop();
						}
						//不符合汉诺塔规则 扔回去
						else {
							books[plate].top().direct_drop(cent_plate_x[plate], books[plate].top().centofy);
						}
					}
				}
			}
			//点不中就无法跟随
			if (!is_follow) {
				plate = 3 * msg.x / width;
				if(books[plate].size()) {
					if (books[plate].top().if_in_cube(msg.x, msg.y)) {
						books[plate].top().temp_transfer_to();
						change_bool(&is_follow); 
						break;
					}
				}
			}
		}
				break;
			case WM_LBUTTONUP:
				break;
			case WM_MOUSEMOVE:   //鼠标移动
				//跟随鼠标
				if (is_follow){
					books[plate].top().draw_follow(msg.x,msg.y,0.010);
					FlushBatchDraw();
				}
				break;
			}
		Sleep(10);
		if (books[2].size() == numofbooks)break;

	}
	
	//清空栈
	for (int i = 0; i < 3; i++) {
		while (!books[i].empty()){books[i].pop();}
	}
	top_y[0] = base_y;
	cleardevice();
}

//关卡
void main_1(int numofbooks){
	books_init(numofbooks);
	Sleep(100);
	move_books(numofbooks);
}


int main() {

	for (int i = 1; i <= 6; i++) {

		initgraph(width, height);
		draw_all_lines();
		main_1(i);
		closegraph();
	}

	return 0;
}

/*
参考文献;
	CSDN https://blog.csdn.net/qq_33866593/article/details/105125118
*/
