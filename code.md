---


---

<h3 id="reason">reason</h3>
<p>This snippet of code is written for a project-oriented simulation about how a split keyboard layout affects the location, shape and square of the keyboard button for each letter. The theory isn’t difficult but the calculation is tedious by hand so it is hard to debug. It taught me to implement some complicated examples to validate the correctness of algorithm which in other scenarios I’ve not encountered before.  Also, the simulation is a rather time-consuming process and needs multi-threading. I didn’t notice this problem in the 1st place and caused some concurrency failures.  I think these lines of code taught me a lot practical skills that lesson assignment did not stress.</p>
<h3 id="code">code</h3>
<pre><code>void maxCR(string* qwertyHandArray, int* LR, int* LC, int* RR, 
int* RC, double* sizeL, double* sizeR, double UR, double UL,
double* coor_L_row,double* coor_L_column, double* coor_R_row,double* coor_R_column
) {
	*LR = 0;
	*RR = 0;

	int maxL1 = -1, minL1 = 30;
	int maxL2 = -1, minL2 = 30;
	int maxL3 = -1, minL3 = 30;

	int maxR1 = -1, minR1 = 30;
	int maxR2 = -1, minR2 = 30;
	int maxR3 = -1, minR3 = 30;

	float maxDL1 = -1,minDL1 = 30;
	float maxDL2 = -1,minDL2 = 30;
	float maxDL3 = -1,minDL3 = 30;

	float maxDR1 = -1,minDR1 = 30;
	float maxDR2 = -1,minDR2 = 30;
	float maxDR3 = -1,minDR3 = 30;
	
	float minRL = 4,maxRL = -1;
	float minRR = 4,maxRR = -1;

	// first row
	for (int i = 0; i &lt; 10; i++) {
		int char_idx = qwerty[i] - 'a';
		if ((*qwertyHandArray)[i] == '0') {
			*LR = 1.0;
			
			maxRL = maxRL &lt; *LR ? *LR : maxRL;
			minRL = minRL &lt; *LR ? minRL : *LR;

			maxDL1 = maxDL1 &lt; coor_org_column[char_idx]? coor_org_column[char_idx]:maxDL1;
			minDL1 = minDL1 &gt; coor_org_column[char_idx]? coor_org_column[char_idx]:minDL1;


		} else {
			*RR = 1.0;

			maxRR = maxRR &lt; *RR ? *RR : maxRR;
			minRR = minRR &lt; *RR ? minRR : *RR;



			maxDR1 = maxDR1 &lt; coor_org_column[char_idx]? coor_org_column[char_idx]:maxDR1;
			minDR1 = minDR1 &gt; coor_org_column[char_idx]? coor_org_column[char_idx]:minDR1;
			
		}
	}
	// second row
	for (int i = 10; i &lt; 19; i++) {
		int char_idx = qwerty[i] - 'a';
		if ((*qwertyHandArray)[i] == '0') {
			*LR = 2*1.0+1.0;

			maxRL = maxRL &lt; *LR ? *LR : maxRL;
			minRL = minRL &lt; *LR ? minRL : *LR;

			maxDL2 = maxDL2 &lt; coor_org_column[char_idx]? coor_org_column[char_idx]:maxDL2;
			minDL2 = minDL2 &gt; coor_org_column[char_idx]? coor_org_column[char_idx]:minDL2;
			

		} else {
			*RR = 2*1.0+1.0;

			maxRR = maxRR &lt; *RR ? *RR : maxRR;
			minRR = minRR &lt; *RR ? minRR : *RR;

			maxDR2 = maxDR2 &lt; coor_org_column[char_idx]? coor_org_column[char_idx]:maxDR2;
			minDR2 = minDR2 &gt; coor_org_column[char_idx]? coor_org_column[char_idx]:minDR2;
			
		}
	}
	// third row
	for (int i = 19; i &lt; 26; i++) {
		int char_idx = qwerty[i] - 'a';
		if ((*qwertyHandArray)[i] == '0') {
			*LR = 2*2.0+1.0;

			maxRL = maxRL &lt; *LR ? *LR : maxRL;
			minRL = minRL &lt; *LR ? minRL : *LR;

			maxDL3 = maxDL3 &lt; coor_org_column[char_idx]? coor_org_column[char_idx]:maxDL3;
			minDL3 = minDL3 &gt; coor_org_column[char_idx]? coor_org_column[char_idx]:minDL3;
			
		} else {
			*RR = 2*2.0+1.0;

			maxRR = maxRR &lt; *RR ? *RR : maxRR;
			minRR = minRR &lt; *RR ? minRR : *RR;

			maxDR3 = maxDR3 &lt; coor_org_column[char_idx]? coor_org_column[char_idx]:maxDR3;
			minDR3 = minDR3 &gt; coor_org_column[char_idx]? coor_org_column[char_idx]:minDR3;
			
		}
	}

	float maxDL = findMaxIn3(maxDL1,maxDL2,maxDL3);
	float minDL = -findMaxIn3(-minDL1,-minDL2,-minDL3);

	float maxDR = findMaxIn3(maxDR1,maxDR2,maxDR3);
	float minDR = -findMaxIn3(-minDR1,-minDR2,-minDR3);
	
	*LC = maxDL - minDL + 2.0; // find the length of left bounding box	
	*RC = maxDR - minDR + 2.0; // find the length of right bounding box
	*LR = maxRL - minRL + 2.0; //find the height of the left bounding box
	*RR = maxRR - minRR + 2.0; //find the height of the right bounding box

	//wL: width of the key block on left(on row dimension)
	//hL: height of the key block on left(on column dimension)
	double cL = 2.0*UL / *LC;
	double rL = 2.0*UR / *LR;

	//wR: width of the key block on right(on row dimension)
	//hR: height of the key block on right(on column dimension)
	double cR = 2.0*UL / *RC;
	double rR = 2.0*UR / *RR;

	*sizeL = rL * cL;
	*sizeR = rR * cR;

	for (int i = 0; i &lt; 26; i++) {
		int char_idx = qwerty[i] - 'a';
		if ((*qwertyHandArray)[i] == '0') {
			coor_L_row[char_idx] = (coor_org_row[char_idx]-minRL+1.0) * UR/ *LR;
			coor_L_column[char_idx] = (coor_org_column[char_idx]-minDL+1.0)* UL / *LC;
		} else {
			coor_R_row[char_idx] = (coor_org_row[char_idx]-minRR+1.0) * UR / *RR;
			coor_R_column[char_idx] = (coor_org_column[char_idx]-minDR+1.0 )* UL / *RC;
		}
	}
}
</code></pre>
<blockquote>
<p>Written with <a href="https://stackedit.io/">StackEdit</a>.</p>
</blockquote>

