#include "Header.h" 

//ЯВНЫЙ МЕТОД ЭЙЛЕРА

double functions1(double* u, double t, int n)
{
	if (n == 0) {
		return -u[0] * u[1] + ((t < 1e-9) ? 0.0 : (sin(t) / t));
	}
	else if (n == 1) {
		return -u[1] * u[1] + (3.125 * t) / (1 + t * t);
	}
	return 0;
}

void ExplicitMethod(double* u, int n)
{
	ofstream outT;
	ofstream outU1;
	ofstream outU2;
	outT.open("ExplicitMethodT.txt");
	outU1.open("ExplicitMethodU1.txt");
	outU2.open("ExplicitMethodU2.txt");
	ofstream ouT;
	ouT.open("ExplicitMethoD.txt");
	// исходные данные
	double tau = 0, tk = 0;
	double Eps = 0.001, T = 1, tauMax = 0.01;
	double* yk = new double[n];
	int kol = 0;

	for (int i = 0; i < n; i++) // присваиваем yk = u0
		yk[i] = u[i];

	//вывод в файл загаловка таблицы
	/*for (int i = 0; i < n; i++)
	{
		if (i == 0)
		{
			out << "    t" << setw(15) << "    u" << i + 1 << setw(14);
		}
		else if (i == n - 1)
			out << "    u" << i + 1 << endl;
	}*/

	do {
		double* tmp = new double[n];
		for (int i = 0; i < n; i++)
		{
			tmp[i] = functions1(yk, tk, i);
		}
		//  определение шага по формулам 3.11, 3.12 
		// выбор минимального
		if (Eps / (abs(tmp[0]) + Eps / tauMax) > Eps / (abs(tmp[1]) + Eps / tauMax))
		{
			tau = Eps / (abs(tmp[1]) + Eps / tauMax);
		}
		else
		{
			tau = Eps / (fabs(tmp[0]) + Eps / tauMax);
		}

		for (int i = 0; i < n; i++)  // выполняем шаг yk=yk+Tau*f[yk,tk]
		{
			yk[i] += tau * fabs(tmp[i]);
		}
		tk += tau;

		for (int i = 0; i < n; i++) // записываем в файл
		{
			if (i == 0)
				ouT << tk << setw(15);
			ouT << setw(15) << yk[i];
			if (i == n - 1)
				ouT << endl;
		//outT << tk << endl;
		//outU1 << yk[0] << endl;
		//outU2 << yk[1] << endl;
		}
		if (tk + tau > T && tk < T) // если tk перепрыгнула интервал T, то смещаем tk в T-Tau
		{
			tk = T - tau;
		}
		kol++;
	} while (tk < T);
	cout << "Количество итраций: " << kol << endl;
	outT.close();
	outU1.close();
	outU2.close();
	ouT.close();
	delete[] yk;
}
