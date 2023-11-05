#! /bin/bash
echo User Name: 구현규
echo Student Number: 12194114
echo [ MENU ]
echo "--------------------------
1. Get the data of the movie identified by a specific 'movie id' from 'u.item'
2. Get the data of action genre movies from 'u. item'
3. Get the average Spect themover id Fatigda the movie identified by
4. Delete the 'IMDb URL' from 'u.item'
5. Get the data about users from'u.user'
6. Modify the format of 'release date' in 'u.item'
7. Get the data of movies rated by a specific 'user id' from 'u.data'
8. Get the average 'rating' of movies rated by users with 'age' between 20 and 29 and 'occupation' as 'programmer'
9. Exit
--------------------------"
while true
read  -p "Enter your choice [ 1-9 ] " n
do
    case $n in
      1) echo ""
         read  -p "Please enter 'movie id'(1~1682):" id
         echo ""
         cat $1 | awk -F"|" -v num=$id '$1==num {print $0}'
         echo ""
         ;;
      
      2) echo ""
         read -p "Do you want to get the data of 'action' genre movies from 'u.item'?(y/n):" ans
         echo ""
         if [ $ans = "y" ]
         then
            cat $1 | awk -F"|" '$7==1 {print $1,$2}'|sort -n| head -n 10
         fi
         echo ""
         ;;
      
      3) echo ""
         read  -p "Please enter the 'movie id'(1~1682):" id
         echo ""
         data=$(cat $2 | awk -v num=$id '$2==num {print $3}')
         save=0
         count=0
         for var in $data
         do
            save=$(( save+$var ))
            count=$(( count+1 ))
         done
         echo $save $count $id | awk '{printf "average rating of %d: %.6g \n", $3, $1 / $2}'
         echo ""
         ;;
      
      4) echo ""
         read -p "Do you want to delete the 'IMDb URL' from 'u.item'?(y/n):" ans
         echo ""
         if [ $ans = "y" ]
         then
            cat $1 | sed -E 's/http[^\)]+\)//g' | head -n 10
         fi
         echo ""
         ;;
      
      5) echo ""
         read -p "Do you want to get the data about users from 'u.user'?(y/n):" ans
         echo ""
         if [ $ans = "y" ]
         then
            cat $3 | sed -Ee 's/\|/ /g' -e 's/M/male/g' -e 's/F/female/g' -e 's/[0-9]{5}/ /g' -e 's/([0-9]+) ([0-9]+) /users \1 \2 years old /' | head -n 10
         fi
         echo ""
         ;;
      
      6) echo ""
         read -p "Do you want to Modify the format of ‘release data’ in ‘u.item’?(y/n):" ans
         echo ""
         if [ $ans = "y" ]
         then
            data1= cat $1 | tail -f | sed -Ee 's/(.*)(\)\|)([0-9]{2})(-[A-Z][a-z]{2}-)([0-9]{4})(.*)/\1\2\5\4\3\6/g' -Ee 's/-Jan-/01/g' -Ee 's/-Feb-/02/g' -Ee 's/-Mar-/03/g' -Ee 's/-Apr-/04/g' -Ee 's/-May-/05/g' -Ee 's/-Jun-/06/g' -Ee 's/-Jul-/07/g' -Ee 's/-Aug-/08/g' -Ee 's/-Sep-/09/g' -Ee 's/-Oct-/10/g' -Ee 's/-Nov-/11/g' -Ee 's/-Dec-/12/g'
            echo $data1
         fi
         echo ""
         ;;
      
      7) echo ""
         read  -p "Please entrer the 'user id'(1~943):" id
         echo ""
         data=$(cat $2 | awk -v num=$id '$1==num {print $2}' |sort -n)
         data1=$(cat $2 | awk -v num=$id '$1==num {print $2}'|sort -n|head -n 10)
         echo $data | sed -E 's/ /|/g'
         echo ""
         for var in $data1
         do
            data3=$( cat $1 | awk -F"|" -v num=$var '$1==num {print $1 "|" $2}' )
            echo $data3
         done
         echo ""
         ;;
      
      8) echo ""
         read -p "Do you want to rest the average 'rating' of movies rated by users with 'age' between 20 and 29 and 'occupation' as 'programmer'?(y/n):" ans
         echo ""
         if [ $ans = "y" ]
         then
            data=$(cat $3 | awk -F"|" '$4=="programmer" {print $1 "|" $2}' | sed -En '/\|2[0-9]/p' | sed -E 's/\|2[0-9]//g')
            for var in $( seq 1 1682 )
            do
               data1=
               for var1 in $data
               do
                  data2=$(cat $2 | awk -v num=$var '$2==num {print $1 " " $3 }' |awk -v num1=$var1 '$1==num1 {print $2 " " }')
                  data1+=$data2
               done
               sum=0
               count=0
               for var2 in $data1
               do
                  sum=$(( sum+$var2 ))
                  count=$(( count+1 ))
               done
               if [ $count -ne 0 ]
               then
                  echo $sum $count $var | awk '{printf "%d %.6g \n", $3, $1 / $2}'
               fi
            done
         fi
         echo ""
         
         ;;
      
      9) echo Bye!
         break
         ;;
    esac
done
