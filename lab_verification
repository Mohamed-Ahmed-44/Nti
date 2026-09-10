module array_tasks_tb;
  initial begin
    int arr[] = '{9,7,4,6,2,8,6,5};
    int pivot, i_idx, temp;
    
    pivot = arr[arr.size()-1];
    i_idx = -1;
    
    for (int j = 0; j < arr.size() - 1; j++) begin
      if (arr[j] < pivot) begin
        i_idx++;

        temp = arr[i_idx];
        arr[i_idx] = arr[j];
        arr[j] = temp;
      end
    end
    

    temp = arr[i_idx+1];
    arr[i_idx+1] = arr[arr.size()-1];
    arr[arr.size()-1] = temp;
    
    $display("Q1) Array Partition Result: %p", arr);
  end

  // ==========================================
  // Q2: Max Consecutive Numbers (1s)

  initial begin
    int arr[] = '{1,1,0,1,1,1,1,0,0,0,1};
    int max_count = 0;
    int current_count = 0;
    
    foreach (arr[i]) begin
      if (arr[i] == 1) begin
        current_count++;
        if (current_count > max_count) max_count = current_count;
      end else begin
        current_count = 0;
      end
    end
    
    $display("Q2) Max Consecutive 1s: %0d", max_count);
  end

  // ==========================================
  // Q3: Second Max Number
  
  initial begin
    int arr[] = '{45,34,67,89,78};
    int max1 = 0; 
    int max2 = 0;
    
    foreach (arr[i]) begin
      if (arr[i] > max1) begin
        max2 = max1;
        max1 = arr[i];
      end else if (arr[i] > max2 && arr[i] != max1) begin
        max2 = arr[i];
      end
    end
    
    $display("Q3) Second Max Number: %0d", max2);
  end

  // ==========================================
  // Q4: Frequency Counter
 
  initial begin
    int arr[] = '{8,3,3,4,5,6,3,5,4,6,8,7,6,4,3,5,6};
    

    int freq[int]; 
    
    foreach (arr[i]) begin
      if (freq.exists(arr[i])) begin
        freq[arr[i]]++;
      end else begin
        freq[arr[i]] = 1;
      end
    end
    
    $display("Q4) Frequency Counter:");
    foreach (freq[key]) begin
      $display("  %0d --> %0d", key, freq[key]);
    end
  end

endmodule
