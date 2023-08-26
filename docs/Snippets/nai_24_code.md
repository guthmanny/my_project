以line1/mic1为例：
先通过sig.mic1设定env[0].db[0]的值：

## 包络检测 Peak Detector 子程序
```c
myfunc_sp_env_vol_v2(sig.mic1,&envvol[0],BUFFER_L);

void myfunc_sp_env_vol_v2(float *x,SUACVOLDISP *t,int len)
{
	float sig;
	float at,rt,env,envmax;

	at = t->env_h[0]; rt = t->env_h[1];
	env = t->coe[0]; envmax = 0;

	for (int i = 0; i<len; i++)
	{
		sig = fabs(x[i]);
		if(sig > env)
		    env = sig;
		else  env = env + rt * (sig - env);

		if(env>envmax)
		    envmax = env;
	}
	t->db[0] = 20*log10(envmax + 1.0e-6);
	t->coe[0] = env;
}
```

## 再通过dB2Vu设置Dsp.dp->disp_mic1的值，timset.t1s=设定的采样值

```c
void dB2Vu(void)
{
	loofor_VuValue1(envvol[0].db[0],-30.0f,24.0f,(int *)&Dsp.dp->disp_mic1);
	loofor_VuValue1(envvol[1].db[0],-30.0f,24.0f,(int *)&Dsp.dp->disp_mic2);

	if(envvol[0].db[0]>=-0.1f)
	    Dsp.dp->disp_mic1max = timset.t1s;

	if(envvol[1].db[0]>=-0.1f)
	    Dsp.dp->disp_mic2max = timset.t1s;
}
void loofor_VuValue1(float db,float min,float range,int *res)
{
	float ftmp;
	int   itmp;

	ftmp = (db - min)*5.0f/range;
	if(ftmp<0.0001f) ftmp = 0.0001f;
	itmp = (int)(ftmp*255);
	res[0] = itmp;
}
```
最后通过setled_InVu函数进行灯的控制
```c
setled_InVu(Dsp.dp->disp_mic1, Dsp.dp->disp_mic2,Dsp.dp->disp_mic1max, Dsp.dp->disp_mic2max);

void setled_InVu(int vu1,int vu2,int vu1max,int vu2max)
{
	for(int i=0;i<5;i++)
	{
		if(vu1==0){
			setled_ledon_val(Mic1VuLedTable[i],0x00);
			vu1 = 0;
		}
		else if(vu1<=255){
			setled_ledon_val(Mic1VuLedTable[i],vu1);
			vu1 = 0;
		}
		else{
			setled_ledon_val(Mic1VuLedTable[i],0xff);
			vu1 = vu1 - 255;
		}
	}

	if(vu1max>0)
	    etled_ledon_val(Mic1VuLedTable[4],0xff);

	for(int i=0;i<5;i++)
	{
		if(vu2==0){
			setled_ledon_val(Mic2VuLedTable[i],0x00);
			vu2 = 0;
		}
		else if(vu2<=255){
			setled_ledon_val(Mic2VuLedTable[i],vu2);
			vu2 = 0;
		}
		else{
			setled_ledon_val(Mic2VuLedTable[i],0xff);
			vu2 = vu2 - 255;
		}
	}
	if(vu2max>0) setled_ledon_val(Mic2VuLedTable[4],0xff);
}
```
