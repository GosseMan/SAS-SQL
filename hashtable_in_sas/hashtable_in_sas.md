ref) https://support.sas.com/resources/papers/proceedings/pdfs/sgf2008/029-2008.pdf
Hash를 이용한 join. 일정규모 이상의 데이터에서 고효율.
<pre>
<code>
data output_file;
  declare hash map();
    rc = map.definekey('key');
    rc = map.definedata('data');
    rc = map.definedone();
   do until (eof1);
    set kw_raw.mapping_table end = eof1;
    rc = map.add();
   end;
   do until (eof2);
    set input_file end = eof2;
    call missing(data);
    rc = map.find();
    output;
   end;
   stop;
run;
</code>
</pre>
