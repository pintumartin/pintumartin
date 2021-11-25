clear all;close all;
clearvars;
novar=2;
z=[300 400];
cons=[80 120;20 25];
b=[1400;3000];
s=eye(size(cons,1));
A=[cons s b];
cost=zeros(1,size(A,2));
cost(1:novar)=z;
%basic varibles
BV=novar+1:1:size(A,2)-1;
%calculate zj-cj
zjcj=cost(BV)*A-cost;
%printtable
zcj=[zjcj;A];
RUN=true;
while RUN
SimpTable=array2table(zcj);
SimpTable.Properties.VariableNames(1:size(zcj,2))={'x','y','s1','s2','sol'};

 if any(zjcj(:)<0)
   fprintf('   The current table is not optimal\n');
   fprintf('=========THE NEXT ITERATION RESULTS============\n');
   disp(' old basic variables (BV) ');
   disp(BV);
%find the entering variable
zc=zjcj(1:end-1);
[Entercol,Pivcol]=min(zc);
fprintf('The minimum element in zj-cj row is %d corresponding to column %d \n',Entercol,Pivcol);
fprintf('Entering variable is %d \n',Pivcol);
%find the leaving variable
sol=A(:,end);
Column=A(:,Pivcol);
if all(Column<=0)
    error('LPP is unbounded,all entries <=0 in column %d',Pivcol);
else
 
   for i=1:size(Column,1)
      if Column(i)>0
        ratio(i)=sol(i)./Column(i);
      else
        ratio(i)=inf;
      end    
   end
   %find minimun
   [MinRatio,pvt_row]=min(ratio);
   fprintf('minimum ratio corresponding to pivot row is %d \n',pvt_row);
   fprintf('Leaving variable is %d \n',BV(pvt_row));
end   
   BV(pvt_row)=Pivcol;
   disp('New basic variables (BV) ,= ');
   disp(BV);
  %pivot key
  pvt_key=A(pvt_row,Pivcol);
  %update the table for next iteration
  A(pvt_row,:)=A(pvt_row,:)./pvt_key;
  for i=1:size(A,1)
      if i~=pvt_row
          A(i,:)=A(i,:)-A(i,Pivcol).*A(pvt_row,:);
      end
     
  
  zjcj=zjcj-zjcj(Pivcol).*A(pvt_row,:);
     %for printing purpose
     zcj=[zjcj;A];
     Table=array2table(zcj);
     Table.Properties.VariableNames(1:size(zcj,2))={'x','y','s1','s2','sol'};
     %printing purpose
     BFS=zeros(1,size(A,2));
   BFS(BV)=A(:,end);
   BFS(end)=sum(BFS.*cost);
   current_BFS=sum(BFS.*cost);
   current_BFS=array2table(BFS);
   current_BFS.Properties.VariableNames(1:size(current_BFS,2))={'x','y','s1','s2','sol'}; 
  end
 else
    RUN=false;
    fprintf('=======**********==========\n');
    fprintf('Current BFs is optimal\n');
    fprintf('=========**********=========');

 end
end


