#include <iostream>
#include <algorithm>
#include <numeric>
#include <map>
#include <vector>
#include <string>
#include <fstream>
#include <stdexcept>
#include <sstream>
#include <set>

int GetPointTotalForStudent(std::map<std::string, std::string> student_info, std::string keyword) {
    //s is the key word here
    //copy map into a map with only key words
    int size = student_info.size();
    std::vector<int> total_points(size);
    std::fill(total_points.begin(), total_points.end(), 0);
    std::map<std::string, std::string> m_copy; //holds all the keyword keys
    std::transform(student_info.begin(), student_info.end(), total_points.begin(), [&](auto p) {
        if((p.first).find(keyword) != std::string::npos) {
            try {
                return stoi(p.second);
            }   
            catch (...) { //in case p.second cant be converted, we return 0 to avoid errors
                return 0;
            }
        }
        else {
            return 0;
        }
    });
    return std::accumulate(total_points.begin(), total_points.end(), 0);
    
}


int GetTopNHomeworkTotalForStudent(std::map<std::string, std::string> student_info, int number) {
    // "HW"
    int count = 0;
    std::vector<int> hw_vector(student_info.size()); //setting to large size
    std::transform(student_info.begin(), student_info.end(), hw_vector.begin(), [&](auto p) {
        if((p.first).find("HW") != std::string::npos) { //if HW is in the key, then we want to add it
            try {
                count = count + 1;
                return stoi(p.second); //return converted value
            }   
            catch (...) { 
                return 0;
            }
        }
        else {
            return 0;
        }
    });
    std::sort(hw_vector.begin(), hw_vector.end()); //sort
    std::reverse(hw_vector.begin(), hw_vector.end()); //reverse the sort to get highest to lowest
    hw_vector.resize(number); //resize to drop homeworks
    return std::accumulate(hw_vector.begin(), hw_vector.end(), 0); // add everything in vector

}

int GetNumberOfMissingLabsForStudent(std::map<std::string, std::string> student_info) {
    int count = 0;
    std::vector<int> labs(student_info.size()); //setting to large size
    std::transform(student_info.begin(), student_info.end(), labs.begin(), [&](auto p) {
        if((p.first).find("Lab") != std::string::npos) { //if lab is located  and doesnt = 1, then we add to count
            if(p.second != "1") {
                count = count + 1;
                return 0;
            }
            else {
                return 0;
            }
        }
        else {
            return 0;
        }
    });
    return count; //return number of missing labs
}
int GetPointTotalForStudent(std::map<std::string, std::string> student_info) {
    std::vector<int> totals(student_info.size()); //setting to large size
    int count = 0;
    std::transform(student_info.begin(), student_info.end(), totals.begin(), [&](auto p) { 
        if((p.first).find("HW") != std::string::npos) { //if HW is located, add so we know total num of homeworks in stu info
            count = count + 1;
        }
        if((p.first).find("Lab") == std::string::npos && (p.first).find("Project Honors") == std::string::npos) { //dont add labs or honors proj to total
            try {
                return stoi(p.second); //return converted value
            }
            catch(...) {
                return 0;
            }
        }
        else {
            return 0;
        }
        
    });
    int sum = std::accumulate(totals.begin(), totals.end(), 0); // total points
    sum = sum - GetPointTotalForStudent(student_info, "HW"); //total hw points before drop
    sum = sum + GetTopNHomeworkTotalForStudent(student_info, count - 2); //sum + homework points with drop
    return sum;
}


std::map<std::string, std::map<std::string, std::string>> GetIDToInfoFromCSV(std::string file_name) {
    std::ifstream fin(file_name); //open file
    std::map<std::string, std::string> inner_map;
    std::map<std::string, std::map<std::string, std::string>> m; //declare two maps, inner and outer one
    std::vector<std::string> first_row_vec;
    std::string top_line; //first line
    std::istringstream ss;
    // adding everyhting in first row to a vector
    std::string temp;
    std::getline(fin, temp);
    ss.str(temp); //using string stream so i can use getline on string
    while(std::getline(ss, top_line, ',')) {
        first_row_vec.push_back(top_line);
    }
    //vector now contains first line separated into words

    int count = 0;
    std::string ID_VALUE;
    std::string temp2;
    std::string each_line; //initializing vars
    while(std::getline(fin, temp2)) { //puts each line into temp2
        ss.clear();
        ss.str(temp2); 
        std::vector<std::string> each_line_vec; //same thing as first row, using ss so i can getline a string
        each_line_vec.clear(); 
        int count = 0;
        while(std::getline(ss, each_line, ',')) { //puts each line into a vector of points
            if (each_line.find("notes") != std::string::npos) {
                each_line_vec.push_back(""); //if val is notes, add ""
                count = count + 1;
                continue;
            }
            each_line_vec.push_back(each_line); // add to vec
            if(count == 1) {
                ID_VALUE = each_line;
            } //store id value to add later
            count = count + 1;
        }
        for(int i = 0; i < first_row_vec.size(); ++i) {
            inner_map[first_row_vec[i]] = each_line_vec[i]; //append the vectors into map
            m[ID_VALUE] = inner_map; //append map above to bigger map
        }
        
    }
    return m;
}
std::map<std::string, double> GetIDToGrade(std::map<std::string, std::map<std::string, std::string>> m) {
    std::vector<std::string> v(m.size()); //large size of vector
    std::string ID_VALUE;
    std::map<std::string, double> return_map; //initializing return map
    std::map<std::string, std::string> student_info;
    int point_total;
    std::transform(m.begin(), m.end(), v.begin(), [&](auto p) {
        ID_VALUE = p.first;
        student_info = p.second;
        point_total = GetPointTotalForStudent(student_info); //geting total points
        double double_total = static_cast<double>(point_total);
        int labs = GetNumberOfMissingLabsForStudent(student_info); //getting missing labs
        int labs_over_two = labs - 2; //every lab over 2 makes you lose 0.5 of ur gpa
        double gpa_reduction = 0;
        if(labs_over_two > 0) {
            gpa_reduction = 0.5 * labs_over_two;
        }
        double GPA;
        if(double_total >= 900) { //determines the GPA based on points
            GPA = 4.0;
        }
        else if(double_total >= 850) {
            GPA =  3.5;
        }
        else if(double_total >= 800) {
            GPA = 3.0;
        }
        else if(double_total >= 750) {
            GPA = 2.5;
        }
        else if(double_total >= 700) {
            GPA = 2.0;
        }
        else if(double_total >= 650) {
            GPA = 1.5;
        }
        else if(double_total >= 600) {
            GPA = 1.0;
        }
        else {
            GPA = 0;
        }
        GPA = GPA - gpa_reduction;
        if (GPA < 0) { //if GPA is already 0, dont subtract labs
            GPA = 0;
        }
        return_map[ID_VALUE] = GPA;
        return 0;
    });
    return return_map;
}
std::set<std::string> GetStudentsEligibleForHonorsCredit(std::map<std::string, std::map<std::string, std::string>> large_map, int n) {
    std::set<std::string> s;
    std::vector<std::string> v(99); // large size fo r throwaway vector
    std::transform(large_map.begin(), large_map.end(), v.begin(), [&](auto p){
        std::string ID = p.first;
        double GPA = 0;
        std::map<std::string, std::string> student_info = p.second;
        int projectScore = GetPointTotalForStudent(student_info, "Project Honors"); //find out the points on the Honors proj
        std::map<std::string, double> GPA_map = GetIDToGrade(large_map);
        std::transform(GPA_map.begin(), GPA_map.end(), v.begin(), [&](auto p1) { //need second transform to get GPA
            if(ID == p1.first) { //if the ID here is the same as the ID above, we know its the same student
                GPA = p1.second; 
            }
            return 0;
        });
        if(GPA >= 3.5 && projectScore >= n) { //this is what determines if they are eligible
            s.insert(ID);
        }
        return 0;
    });
    return s;
}


int main() {
  
    return 0;
}





/*

example.csv contents:
Name,ID,Midterm Exam,Final Exam,HW 06,HW 01,HW 02,HW 03,HW 04
Josh N,A12345,66,115,20,15,,14,28
Emily D,Z2468,67,125,,0,20,20,29





Name,ID,Midterm Exam,Final Exam,HW 01,HW 02,HW 03,HW 04,HW 05,HW 06,HW 07,HW 08,HW 09,HW 10,HW 11,HW 12,HW 13,HW 14,HW 15,HW 16,HW 17,Lab 01,Lab 02,Lab 03,Lab 04,Lab 05,Lab 06,Lab 07,Lab 08,Lab 09,Lab 10,Lab 11,Lab 12,Lab 13,Lab 14,Project 1,Project 2,Project 3,Project Honors,Notes
Josh N,A12345,98,133,16,9,10,28,29,24,22,1,1,29,0,10,12,28,22,26,3,0,1,1,0,1,0,1,1,1,0,0,0,1,0,60,93,112,6,Secretly the instructor
Emily D,Z2468,85,129,12,27,19,2,20,29,11,30,0,15,4,2,8,7,12,15,22,1,0,0,1,0,1,0,0,0,0,0,0,1,1,50,95,109,2,Takes good notes
Rich E,A67686,90,130,8,25,12,14,17,13,26,0,9,19,18,22,24,2,16,13,23,0,1,1,1,0,0,0,0,1,1,1,1,0,0,67,85,122,10,
Charles O,Z2763,82,,25,24,29,2,9,7,8,6,24,26,8,0,3,21,NA,15,19,0,1,0,0,0,1,1,0,1,0,Not Present,1,1,0,59,92,120,0,

*/
