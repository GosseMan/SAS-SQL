# SAS Data step vs PROC SQL
reference) Why choose between SAS Data Step and PROC SQL when you can have both? (SAS)

##### 1. Reading raw data
SAS의 data step은 raw data를 읽을 수 있다.

##### 2. Joining data
SAS data step merge
: merging technique 사용

PROC SQL
: join algorithm 사용

PROC SQL이 유연하게 사용 가능하다(join하는 column이 같은 이름이 아니어도 됨, join 조건이 equality가 아니어도 됨, pre-sorted data가 아니어도 됨)

비교(sas data step merge vs proc sql)

데이터셋 크기 : 디스크 공간 외의 제약이 없음 vs 최대 256 table
데이터 처리 : sequential so that observations with duplicate
BY values are joined one-to-one. vs Cartesian product for duplicate BY
values.
출력 데이터셋 : 여러개 만들어질 수 있음 vs 하나만 만들어질 수 있음
complex logic : if then/select when 사용 vs case 사용(비교적 덜 유연함)
정렬 : 정렬이 필수 vs 필수가 아님
join 조건 : equality vs inequal join도 가능
same name variables : Same named BY variables must be available in
all data sets vs Same named variables do not
have to be in all data sets.

##### 3. Accumulating data
SAS data step이 + 기호 사용만으로 훨씬 간단하게 수행 가능.
PROC SQL의 경우 row number를 나타내는 _n_을 이용해서 더 복잡하게 수행 가능.

SAS data step example code
<pre>
<code>
Data step accumulating data
data dsrunning;
set shoes;
keep region product sales
running_total;
running_total + sales;
run;
</code>
</pre>

PROC SQL example code
<pre>
<code>
Proc sql accumulating data
data shoes;
set sashelp.shoes;
obs=_n_;
run;
proc sql;
create table sqlrunning as
select region, product, sales,
(select sum(a.sales) from shoes as a
where a.obs <= b.obs) as Running_total
from shoes as b;
quit;
</code>
</pre>

##### 4. Aggregating data
PROC SQL이 boolean 방식으로 logic을 표현하기 용이하다.

SAS data step example code
<pre>
<code>
Data step aggregating data
/*1. prep data for summarizing*/
data dsboolean;
set bweight;
by visit;
if first.visit then do;
wgt4000=0;
wle2500=0;
end;
if weight > 4000 and married=1 and
momsmoke=1 then wgt4000 + 1;
else if weight <=2500 and married=1
and momsmoke=1 then wle2500 + 1;
if last.visit;
label wgt4000 ='over average weight'
 wle2500 ='under average weight';
keep visit wgt4000 wle2500;
run;
</code>
</pre>

PROC SQL example code
<pre>
<code>
Proc Sql aggregating data
proc sql;
create table sqlboolean as
select visit,
sum(weight > 4000 and married=1
and momsmoke=1) as wgt4000 'over
average weight',
sum(weight <=2500 and married=1Cancel changes
and momsmoke=1) as wle2500 'under
average weight'
from sashelp.bweight
group by visit;
quit;
</code>
</pre>

##### 5. Managing data
PROC SQL의 dictionary table을 이용하면 metadata를 빠르게 가져올 수 있다.

SAS data step example code
<pre>
<code>
proc sql;
select libname, memname, name, type, length
from dictionary.columns
where upcase(name) contains 'ID'
and libname='SASHELP' and type='num';
quit;
</code>
</pre>

PROC SQL example code
<pre>
<code>
data dsdict;
set sashelp.vcolumn;
keep libname memname name type length;
where upcase(name) contains 'ID' and libname='SASHELP' and type='num';
run;
</code>
</pre>
